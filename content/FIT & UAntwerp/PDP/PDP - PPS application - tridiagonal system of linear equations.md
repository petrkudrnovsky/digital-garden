
> [!tldr] First 5 minutes of hell
> Every equation $f_i x_{i-1} + g_i x_i + h_i x_{i+1} = b_i$ (which connects three consecutive neighbors in a chain) could be written as "multiply by a small matrix". Then when we will have all 3x3 matrices for each equation, then we could use PPS to do all the multiplications in parallel (and not sequentially, which is slow). 
> - the form of the matrix is below
> 
> Using PPS (prefix sum), we will calculate the $H_i$ matrices (which are just cumulative multiplications of $G_i$, ... $G_2$, $G_1$). The starting vector ($x_1, 0, 1$ is the same every time, just the $H_i$ changes).
> - this computation of all running products $H_1$, $H_2$, ... are parallelizable in $O(n/p + \log p)$
> 
> Whole algorithm:
> - compute all $H_i$ in parallel using prefix sums on matrices (each thread can find it's own $G_i$ from it's own equation)
> - one processor solves a tiny equation system to find the $x_1$ and the broadcasts it to all processes (OAB = one-to-all broadcast)
> 	- takes a $O(\log p)$ rounds
> - each processor can compute it's $x_{i+1}$ values independently (it has the $x_1$ value and the $H_i$)
> 	- it solves a 3 equations with 3 unknowns (done in constant time by a good solver)
> 	- $\begin{bmatrix} x_{i+1} \\ x_i \\ 1 \end{bmatrix} = H_i \begin{bmatrix} x_1 \\ 0 \\ 1 \end{bmatrix}$
> 
> Since the matrix multiplications are associative, they could be applied like this in a row (to product $H_i$ products and therefore this could be easily parallelized).
### Problem statement

A tridiagonal system of $n$ linear equations has the form $$
\begin{aligned}
g_1 x_1 + h_1 x_2 &= b_1 \\
f_2 x_1 + g_2 x_2 + h_2 x_3 &= b_2 \\
&\;\;\vdots \\
f_i x_{i-1} + g_i x_i + h_i x_{i+1} &= b_i \\
&\;\;\vdots \\
f_n x_{n-1} + g_n x_n &= b_n
\end{aligned}
$$ The defining property: every equation contains a linear combination of at most three consecutive unknowns, with non-zero coefficients only on the main diagonal of the coefficient matrix and on its left and right neighbouring diagonals. The coefficient matrix is thus sparse with non-zeros on exactly three diagonals around the main one, which is why the system is called _tridiagonal_.

The first and last equations are the boundary cases: the first equation has no $f_1 x_0$ term (and the algorithm later treats $x_0 = 0$ by convention), and the last equation has no $h_n x_{n+1}$ term.

### The main trick: recurrent matrix-vector form

Each interior equation $f_i x_{i-1} + g_i x_i + h_i x_{i+1} = b_i$ can be rewritten as $$
\begin{bmatrix} x_{i+1} \\ x_i \\ 1 \end{bmatrix} = G_i \begin{bmatrix} x_i \\ x_{i-1} \\ 1 \end{bmatrix},
$$ where $$
G_i = \begin{bmatrix}
-\dfrac{g_i}{h_i} & -\dfrac{f_i}{h_i} & \dfrac{b_i}{h_i} \\
1 & 0 & 0 \\
0 & 0 & 1
\end{bmatrix}, \qquad 1 \leq i \leq n-1.
$$ Why this works. The first row of $G_i$ is uniquely determined by the coefficients of the $i$-th equation: solving the equation for $x_{i+1}$ yields $x_{i+1} = -(g_i/h_i), x_i - (f_i/h_i), x_{i-1} + b_i/h_i$, which is exactly what the first row of the matrix product produces. The second row carries $x_i$ forward into the next state vector. The third row is a constant $1$, used to encode the affine term $b_i/h_i$ inside what would otherwise be a linear map.

Vectors have a fixed structure. The first two entries of each state vector are two consecutive unknowns $(x_i, x_{i-1})$, and the third entry is always $1$. The third entry being constant is what makes the recurrence well-formed: it keeps the affine-as-linear encoding stable across substitutions.

Iterated substitution. By systematically substituting the recurrence into itself, the state vector at position $i+1$ can be expressed in terms of the boundary state vector at the bottom of the chain: $$
\begin{bmatrix} x_{i+1} \\ x_i \\ 1 \end{bmatrix} = H_i \begin{bmatrix} x_1 \\ 0 \\ 1 \end{bmatrix}, \quad \text{where} \quad H_i = G_i G_{i-1} \cdots G_1, \quad 1 \leq i \leq n-1. \tag{1}
$$ The boundary state vector is $(x_1, x_0, 1)^T = (x_1, 0, 1)^T$ by definition (there is no $x_0$ in the system, so it is fixed to $0$). The vector on the right-hand side of equation (1) is therefore the _same constant vector_ for every $i$. Only the matrix $H_i$ varies with $i$, and it is just the cumulative product of the $G_j$ matrices from $j = 1$ up to $j = i$.

The associative operator. The substitution operator is matrix-matrix multiplication on the fixed-size $3 \times 3$ matrices $G_i$. Matrix multiplication is associative (though not commutative), and the product of two $3 \times 3$ matrices is again a $3 \times 3$ matrix. So computing all the $H_i$ from $[G_1, \ldots, G_{n-1}]$ is a PPS over the array of matrices with operation = matrix multiplication.

The lecturer's broader observation. This trick generalizes: any recurrence relation, any sequence defined recursively, can be solved by prefix computation as long as the recurrent step can be expressed as the application of an associative operator. The tridiagonal solver is one concrete example, but the same idea applies to many other recurrences.

### The algorithm

On $p$ processors $P_0, \ldots, P_{p-1}$:

1. _Parallel PPS on matrices._ Apply a PPS to the array $[G_1, \ldots, G_{n-1}]$ to compute $[H_1, \ldots, H_{n-1}]$. The associative operation is $3 \times 3$ matrix multiplication.
    - Time: $O(n/p + \log p)$ steps.
2. _Solve for $x_1$ on one processor._ $P_{p-1}$ solves a trivial system of three equations in three unknowns to recover $x_1$. The three equations are the last row of the recurrence at position $n$, $$
\begin{bmatrix} x_n \\ x_{n-1} \\ 1 \end{bmatrix} = H_{n-1} \begin{bmatrix} x_1 \\ 0 \\ 1 \end{bmatrix},
$$ together with the original last equation $f_n x_{n-1} + g_n x_n = b_n$. This gives three equations in the three unknowns $x_{n-1}$, $x_n$, $x_1$. Any sequential solver handles this in constant time.
    - Time: $O(1)$ steps.
3. _Broadcast $x_1$._ $P_{p-1}$ performs OAB (one-to-all broadcast) of $x_1$ to every other processor.
    - Time: $O(\log p)$ rounds.
4. _Parallel back-computation of all $x_{i+1}$._ Once every processor knows $x_1$, it can in parallel evaluate equation (1) for every $i$ in its local range, computing $x_{i+1}$ directly by the matrix-vector product $H_i \cdot (x_1, 0, 1)^T$.
    - Time: $O(n/p)$ steps.

Where to obtain $x_1$. The lecturer made the conceptual issue explicit: equation (1) gives $x_{i+1}$ for every $i$ in fully parallel form, _provided_ we know $x_1$. But $x_1$ is exactly the unknown we lack at first. Step 2 above recovers it by combining the last instance of the recurrence with the original last equation - both involve only the three variables $x_{n-1}$, $x_n$, $x_1$, so they form a small closed system that any single processor can solve sequentially.

### Total parallel time complexity

Summing the four phases: $$ T(n, p) = \underbrace{O!\left(\frac{n}{p} + \log p\right)}_{\text{PPS on matrices}} + \underbrace{O(1)}_{\text{solve for } x_1} + \underbrace{O(\log p)}_{\text{OAB of } x_1} + \underbrace{O!\left(\frac{n}{p}\right)}_{\text{back-compute } x_{i+1}} $$ which simplifies to $$ T(n, p) = O!\left(\frac{n}{p} + \log p\right). $$ This matches the scalability of plain PPS, despite the substantially richer underlying problem.

### Structure of the technique

The general pattern this example illustrates:

1. Rewrite each equation of the system as a fixed-size matrix-vector recurrence whose state vector encodes the local unknowns plus a constant slot for affine terms.
2. The cumulative composition of the recurrent step is then a product of fixed-size matrices, and matrix multiplication is associative.
3. Apply PPS to compute all cumulative products $H_i$ in parallel.
4. Solve a small constant-size system at the boundary to fix the missing initial value.
5. Broadcast that initial value and recover all unknowns in fully parallel form.

This pattern works for any recurrent system whose step can be linearized into a constant-size matrix product. The lecturer was explicit that this is a general principle: _recurrence + associative composition = PPS-solvable in parallel logarithmic time_.

### Potential exam questions

1. Define a tridiagonal system of linear equations. What constraints on the coefficient matrix make a system tridiagonal, and what are the boundary cases at the first and last equations?
2. Show how to rewrite the interior equation $f_i x_{i-1} + g_i x_i + h_i x_{i+1} = b_i$ in the matrix-vector recurrence form. Derive the matrix $G_i$ explicitly and explain the role of each of its three rows.
3. Why is the third entry of the state vector fixed to $1$? What would go wrong without it?
4. Starting from the recurrence $\begin{bmatrix} x_{i+1} \ x_i \ 1 \end{bmatrix} = G_i \begin{bmatrix} x_i \ x_{i-1} \ 1 \end{bmatrix}$, derive equation (1): $\begin{bmatrix} x_{i+1} \ x_i \ 1 \end{bmatrix} = H_i \begin{bmatrix} x_1 \ 0 \ 1 \end{bmatrix}$ with $H_i = G_i G_{i-1} \cdots G_1$. Why is the right-hand side vector the same for every $i$?
5. State the associative operation used in the PPS step. Why is associativity sufficient even though matrix multiplication is not commutative?
6. Give the full four-step parallel algorithm for solving the tridiagonal system on $p$ processors. State the time complexity of each step.
7. Why is $x_1$ initially unknown? Describe the small system that $P_{p-1}$ solves in step 2 and explain which equations and unknowns it involves.
8. Once $x_1$ is known and has been broadcast, how does each processor compute its share of the $x_{i+1}$ values in parallel?
9. Derive the total parallel time complexity $T(n, p) = O(n/p + \log p)$. Which phases dominate, and on which topologies does this bound hold?
10. The lecturer claimed that "any recurrence relation can be solved by prefix computation". Explain this principle in your own words and identify the precondition on the recurrence operator that is required.
11. Compare this PPS application with the carry-look-ahead adder and the packing problem. What structural features do all three share, and where do they differ?
12. Suppose the matrix multiplication PPS were implemented on a hypercube of $p$ processors. What would the PPS step look like in terms of the standard hypercube PPS algorithm, and what is the cost per round?

> [!tldr] The first 5 minutes of hell
> The problem is that the ripple-carry adder propagates the carry flags sequentially from LSB to MSB, taking $\Theta(n)$ time on two $n$-bit vectors.
> 
> The carry-look-ahead adder based on PPS does it in $O(\log n)$.
> ![[Pasted image 20260524212119.png]]
> - at first, the $n$-bit arrays are compared and transformed into $B$
> 	- $g$ - generate (both indexes are 1) - regardless of the carry before
> 	- $s$ - stop/absorb (both indexes are 0) - regardless of the carry before
> 	- $p$ - propagate (10 or 01)
> - the $B$ array is padded with an extra $s$ from the right (to ensure that the initial carry is 0)
> - this $B$ array is then given as input to the PPS on the $(.)$ operation (on the image) producing the $B'$ array
> 	- the operation is non-commutative! (but it is associative at the is enough)
> 	- eventually all $p$ are eliminated and replaced by the $g$ or $s$ (reduction in the sense of PPS) - the $p$ dissolve under repeated application of the $(.)$ operation
> - the array $C$ is just shifting left by one and adding 1 if there is $g$ and 0 if there is $s$
> 	- this array also denotes the carry at the step $i$, which then allows to parallelize the whole adding operation (each thread $i$ can sum the numbers on the $i$-th position and add the carry at the $i$-th position
> 
> Complexity: $O(log(n))$, see Time complexity and structure below


### Problem statement

Input: two $n$-bit binary numbers $$ X = x_{n-1} \ldots x_1 x_0, \qquad Y = y_{n-1} \ldots y_1 y_0. $$ Task: compute the sum $$ Z = X + Y = z_{n-1} \ldots z_1 z_0 $$ where each output bit is $$ z_i = x_i \oplus_2 y_i \oplus_2 c_i $$ with $\oplus_2$ denoting XOR (bitwise binary addition without carry) and $c_i$ the carry into position $i$.

Output: the array of carry bits $C = c_n, \ldots, c_1, c_0$ with $c_0 = 0$.

Goal: compute all carries in $O(\log n)$ parallel steps using _carry lookahead_.

Motivation. The standard ripple-carry adder propagates carries sequentially from the least significant to the most significant bit, taking $\Theta(n)$ time on bit-vectors of length $n$. The carry-look-ahead family of adders does better than linear time; this slide presents the variant built on top of PPS, which achieves $O(\log n)$.

### Encoding bit positions with ${s, p, g}$

The first step is a bitwise, fully parallel transformation that maps each pair $(x_i, y_i)$ to one of three symbols, capturing the _role_ that position $i$ plays with respect to carry propagation from the right: $$
b_i := \begin{cases}
g \;(\text{generate}) & \text{if } x_i = y_i = 1, \\
s \;(\text{stop}) & \text{if } x_i = y_i = 0, \\
p \;(\text{propagate}) & \text{otherwise.}
\end{cases}
$$ Reasoning:

- $g$ (generate): two 1-bits at position $i$ produce a carry $c_{i+1} = 1$ regardless of whether any carry arrives from the right.
- $s$ (stop): two 0-bits at position $i$ produce no carry and absorb any carry coming from the right (so the carry chain ending at position $i$ stops there).
- $p$ (propagate): the combinations $01$ and $10$ produce $c_{i+1} = 1$ if and only if a carry arrived from the right; they neither generate nor stop, they merely pass through whatever carry comes in.

After this transformation, the carry computation reduces to figuring out, for each position $i$, whether the carry chain ending at $i$ has been stopped or generated somewhere to its right; the propagate positions are merely conduits that do not by themselves resolve the carry.

### The associative operation $\odot$ on ${s, p, g}$

Define a binary operation $\odot$ on ${s, p, g}$ by the table (rows = left operand, columns = right operand):

|$\odot$|$s$|$p$|$g$|
|---|---|---|---|
|$s$|$s$|$s$|$s$|
|$p$|$s$|$p$|$g$|
|$g$|$g$|$g$|$g$|

Reading the table:

- Combining $s$ with anything yields $s$: a stop on the left absorbs anything to its right.
- Combining $g$ with anything yields $g$: a generate on the left dominates anything to its right.
- Combining $p$ with $s$ yields $s$; with $g$ yields $g$; with $p$ yields $p$: a propagate inherits the behaviour of its right neighbour.

Observation (slide-level remark from the lecturer): out of the nine entries in the table, only one yields $p$ (namely $p \odot p$). So the operation is _reducing_ in the sense that any input containing both stops/generates and propagates gradually eliminates the propagates as the scan progresses; in $O(\log n)$ steps, every $p$ is replaced by either $s$ or $g$. The worst case for ripple-carry - an alternating $01$/$10$ pattern producing all $p$'s - is precisely where the linear scan really would need $n$ steps; PPS handles even this case in $\log n$ steps because $\odot$ is associative.

Non-commutativity. The order of operands matters: $s \odot g = s$ but $g \odot s = g$. So $\odot$ is not commutative, but PPS only requires associativity, and the slide implicitly takes the array order to define the left-to-right reduction.

### The full algorithm

```
for all i := 0..n-1 do_in_parallel
  b_i := { g  if x_i = y_i = 1,
           s  if x_i = y_i = 0,
           p  otherwise }
compute B' = [b'_{n-1}, ..., b'_0] using
  PPS with operation (.) on the array [b_{n-1}, ..., b_0, s]
for all i := 1..n do_in_parallel
  c_i := 1  iff  b'_{i-1} = g
```

Notes on the algorithm:

- The input to the PPS is the array $[b_{n-1}, \ldots, b_0, s]$ - the ${s, p, g}$ string indexed from the most significant down to the least significant, with an extra $s$ appended on the right to ensure $c_0 = 0$.
- The PPS produces $B' = [b'_{n-1}, \ldots, b'_0]$ where each $b'_i$ is the reduction of the suffix from position $i$ down to position $0$ (plus the trailing $s$) under $\odot$. Because all propagates dissolve under repeated applications of $\odot$, each $b'_i$ is either $s$ or $g$, never $p$.
- The carry into position $i$ is then read off directly: $c_i = 1$ if $b'_{i-1} = g$, else $c_i = 0$. This is again a fully parallel step.

Once the carries are known, the sum bits $z_i = x_i \oplus_2 y_i \oplus_2 c_i$ can be computed in one final parallel step over all bit positions.

Worked example (slide 29). For $$ X = 0,1,0,1,1,0,1,1, \qquad Y = 0,1,1,0,1,0,0,1, $$ the encoded array is $$ B = s,g,p,p,g,s,p,g $$ (with the appended trailing $s$). Applying PPS with $\odot$ yields $$ B' = s,g,g,g,g,s,g,g $$

- every $p$ has been resolved to $s$ or $g$. The carry array then becomes $C = 0,1,1,1,1,0,1,1,0$ and the sum is $Z = 1,1,0,0,0,1,0,0$.

### Time complexity and structure

- Per-bit encoding $(x_i, y_i) \mapsto b_i$: 1 parallel step.
- PPS on the ${s, p, g}$ array of length $n+1$: $O(\log n)$ parallel steps (by any of the PPS algorithms from the parent question - PRAM, hypercube, indirect tree, etc.).
- Carry extraction $c_i := [b'_{i-1} = g]$: 1 parallel step.
- Final XORs $z_i := x_i \oplus_2 y_i \oplus_2 c_i$: 1 parallel step.

Total: $O(\log n)$, dominated by the PPS. The associativity of $\odot$ is the only algebraic property the algorithm relies on; everything else is mechanical bit manipulation. This is why the lecturer's framing was: _"It's that simple. It's just an application of prefix sum. Nothing else, nothing more."_

### Why this is a clean PPS application

Three properties of the carry-look-ahead construction make it an unusually clean illustration of PPS:

1. The original problem (carry propagation) is inherently sequential under the bit-level view, just as the sequential PPS algorithm is inherently sequential.
2. Reformulating the problem over the alphabet ${s, p, g}$ exposes an associative operation $\odot$ that captures the entire propagation logic. Once associativity is in hand, the standard PPS machinery applies verbatim.
3. The reduction is non-trivial in the sense that the alphabet ${s, p, g}$ is not a numeric type and $\odot$ is not commutative - so this is a good example of a real-world PPS that goes beyond integer sums.

### Potential exam questions

1. State the problem solved by a carry-look-ahead adder, including the input, the output (specifically what is computed by the PPS step), and the desired time bound.
2. Define the encoding $b_i \in {s, p, g}$ for each bit position $i$. Justify each of the three cases in terms of what happens to a carry arriving at position $i$.
3. Write out the full $3 \times 3$ table for the operation $\odot$ on ${s, p, g}$. Verify, with a representative case, that $\odot$ is associative.
4. Is $\odot$ commutative? Give a counterexample if not. Why is associativity alone sufficient for the PPS-based algorithm to be correct?
5. Why does the PPS-based carry-look-ahead adder achieve $O(\log n)$ time on inputs that would force a ripple-carry adder to take $\Theta(n)$ time? Identify the worst-case input pattern and explain how $\odot$ resolves it.
6. State the full carry-look-ahead algorithm as pseudocode. Explain why the extra trailing $s$ is appended to the encoded array.
7. After the PPS on $[b_{n-1}, \ldots, b_0, s]$ with operation $\odot$, what are the possible values of each $b'_i$? Justify why $p$ never appears in $B'$.
8. Given $B' = [b'_{n-1}, \ldots, b'_0]$, derive each carry $c_i$. How are the final sum bits $z_i$ computed?
9. Apply the algorithm by hand to $X = 0101,1011$ and $Y = 0110,1001$. Compute $B$, $B'$, $C$, and $Z$.
10. Decompose the total parallel time of the algorithm into its phases. Which phase dominates the time complexity, and on which topologies does the corresponding PPS run optimally?
11. The slide remarks that out of nine entries in the $\odot$ table, only one yields $p$. Why does this fact matter for the algorithm's behaviour?
12. Carry-look-ahead has several known parallel implementations beyond the PPS-based one. What property of $\odot$ makes the PPS variant particularly natural, and what does it have in common with the other PPS-based applications (packing, tridiagonal systems)?
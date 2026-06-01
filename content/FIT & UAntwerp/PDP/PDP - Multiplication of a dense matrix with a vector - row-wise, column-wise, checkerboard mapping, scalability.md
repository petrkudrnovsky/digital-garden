> [!tldr] The big picture
> You need to compute $\vec{y} = A\vec{x}$ with a $(\sqrt{N} \times \sqrt{N})$ matrix on $p$ processors. Each processor only gets a slice of $A$, so it has to talk to others to get the pieces of $\vec{x}$ or $\vec{y}$ it is missing. The three approaches differ only in how $A$ is sliced - row strips, column strips, or square tiles - which changes the communication, not the local work.
> 
> All three end up with the same total cost $T(N, p) \doteq k'_1 \sqrt{N} + k_2 N/p$ and the same scalability $p \leq c\sqrt{N}$.
> - there are $N/p$ multiply adds (computation)
> 	- $k_2$ - how fast the CPU computes (HW constant)
> - $\sqrt{N}$ is the sum of the communication cost (AAB, OAB or reduction)
> 	- $k_1$ - how fast the underlying network moves a number
> - there are $N$ elements in the matrix, therefore $(\sqrt{N} \times \sqrt{N})$

## Problem definition

Given a square $(\sqrt{N} \times \sqrt{N})$-matrix (dense) $A$ and a $(\sqrt{N} \times 1)$-vector $\vec{x}$, compute $$ \vec{y} = A \vec{x}. $$ The matrix is dense, stored as a 2-D array, with $N = n^2$ elements total. We use $N$ as the total number of matrix elements and write the matrix shape as $(\sqrt{N} \times \sqrt{N})$ so that "matrix size" and "number of elements" are unambiguous. Three basic mappings of the dense matrix $A$ to processes are considered:

- row-wise,
- column-wise,
- checkerboard.
- The choice of mapping changes the structure of the algorithm: which data each process initially has, what it needs that it does not have, and what communication is required to obtain the missing data. The local computation count is the same in all three mappings ($\Theta(N/p)$); only the communication pattern differs.

## Row-wise mapping
> [!tldr] Row-wise
> Slice $A$ into horizontal strips - each process gets $\sqrt{N}/p$ full rows. Every process owns only a chunk of $\vec{x}$ but needs the whole vector to do dot products, so first everyone shares their chunk with everyone else (all-to-all broadcast), then each process locally computes its rows of $\vec{y}$.
> - since after the broadcast, each process has the whole $x$ vector, so $P_0$ can calculate the $y_0$ locally etc.
>   
> Clean two-step flow: communicate, then compute. Output $\vec{y}$ lands in the same layout as input $\vec{x}$, which is ideal for iterative methods like the Power Method.
> 
> Scalability: constant efficiency as long as each processor keeps a constant number of rows.
> - $N/p$ has to be kept constant

Processes $P_i$ form a 1-D virtual mesh $M(p)$. Let $$ r = \frac{\sqrt{N}}{p}. $$ Each process is assigned a block of $r$ consecutive rows of $A$. The vectors $\vec{x}$ and $\vec{y}$ are also mapped block-wise across the $p$ processes, each process holding a block of $r$ consecutive elements. Algorithm `RowWiseMVM`$(A, \vec{x}, \vec{y})$:

- Ph.1 (AAB): Each process sends its part of $\vec{x}$ to all other processes (all-to-all broadcast). After this, every process holds the full $\vec{x}$.
- Ph.2 (local compute): For all $P_i$ in parallel, $P_i$ computes $y_j$ for $j = ir, \ldots, (i+1)r - 1$ - that is $r$ dot products, each of length $\sqrt{N}$. The output $\vec{y}$ ends up mapped block-wise across processes in exactly the same way as the input $\vec{x}$. This is important for iterative algorithms like the Power Method that repeatedly apply MVM: no remapping of the vector is needed between iterations.

> Row-wise MVM is the simplest case: one AAB of the vector, then purely local dot products. Communication and computation are cleanly separated.

![[Pasted image 20260525091941.png]]

## Time complexity and scalability of `RowWiseMVM`

Local computation in Phase 2: $$ T_2(N, p) = \Theta(N/p), $$ since each process performs $\sqrt{N}/p$ dot products of vectors of length $\sqrt{N}$, totaling $N/p$ multiply-adds. Communication in Phase 1 is the latency of an AAB of $N/p$ numbers, which depends on the topology and switching technology. Two examples:

- Full-duplex 4-port SF $M(\sqrt{p}, \sqrt{p})$ with noncombining TADT-based AAB: $$ T_1(N, p) = \frac{p}{4}\left(t_s + k_1 \sqrt{N}/p\right) \doteq \frac{k_1 \sqrt{N}}{4}. $$
- Full-duplex 1-port SF $M(\sqrt{p}, \sqrt{p})$ with combining AAB (AAB by dimensions): $$ T_1(N, p) = \sqrt{p},t_s + k_1 \sqrt{p},r + \sqrt{p},t_s + k_1 \sqrt{p},\sqrt{p},r \doteq k_1 \sqrt{N}. $$ In all such cases the total time has the form $$ T(N, p) = T_1(N, p) + T_2(N, p) \doteq k'_1 \sqrt{N} + k_2 \frac{N}{p}. $$ Efficiency: $$ E(N, p) = \frac{k_2 N}{k_2 N + k'_1 p \sqrt{N}} \geq E_0 $$ holds when $$ \sqrt{N} \geq \frac{E_0 k'_1}{(1 - E_0) k_2} \cdot p \quad\Longleftrightarrow\quad p \leq \frac{(1 - E_0) k_2}{E_0 k'_1} \sqrt{N}. $$ Constant efficiency $E_0$ is preserved as long as $p$ grows no faster than $\sqrt{N}$, i.e., $N/p$ can be kept constant - which means a constant number of matrix rows per processor.

> Row-wise MVM has outstanding scalability: constant efficiency can be obtained even with a constant number of matrix rows per processor.

## Column-wise mapping
> [!tldr] Column-wise
> Slice $A$ into vertical strips - each process gets $\sqrt{N}/p$ full columns and the matching chunk of $\vec{x}$. Now each process has everything it needs to compute partial contributions to every element of $\vec{y}$, but no element is complete. So the order flips: compute first, then communicate. Phase 2 runs $p$ simultaneous reductions (one per output chunk, each rooted at a different process) that sum the partials into the final $\vec{y}$.
> - compute part: each process multiplies all elements in the column by the assigned $x_i$ value
> 	- each process produces the partial output of the $y_i$ (but no output is finished)
> 	- to get the complete $y_i$, we have to sum the rows (the partial results)
> - parallel reductions
> 	- all reductions are happening in parallel (each is rooted in a different process, and therefore $y_2$ ends up in the $P_2$) - the pink diagonal
> 
> Same asymptotic cost and scalability as row-wise - the choice between them is usually driven by what fits the surrounding workflow (which mapping fits better a larger workflow).

Processes $P_i$ form a 1-D virtual mesh $M(p)$. Let $r = \sqrt{N}/p$. Initial distribution:

- $P_i$ owns subvector $x_{ir}, \ldots, x_{(i+1)r-1}$ of $\vec{x}$.
- $P_i$ owns columns $ir, \ldots, (i+1)r-1$ of $A$. Final distribution: $P_i$ owns subvector $y_{ir}, \ldots, y_{(i+1)r-1}$. The situation is reciprocal to row-wise: the order of computation and communication is reversed. Algorithm `ColumnWiseMVM`$(A, \vec{x}, \vec{y})$:
- Ph.1 (local compute): For all $i = 0, \ldots, p-1$ in parallel, $P_i$ computes its local contributions to all elements of $\vec{y}$. Each $P_i$ has all rows but only a column-slice of $A$, so it can produce partial values for every $y_j$, but they are incomplete.
- Ph.2 (parallel reductions): For all $i = 0, \ldots, p-1$ in parallel, $P_i$ becomes the root of a row-wise reduction with operation $+$, accumulating the partial dot products from all $P_j$ to produce the final values of $y_{ir}, \ldots, y_{(i+1)r-1}$. Phase 2 consists of $p$ simultaneous reductions, each rooted at a different process, each carrying out the same operation $+$ on different data. On an underlying topology with sufficient bandwidth (e.g., a 1-D SF mesh with pipelining, or any standard hypercube), all $p$ reductions can be executed concurrently. The asymptotic complexity is the same as for row-wise mapping: time complexity and scalability are essentially identical.

> Row-wise and column-wise mappings have the same asymptotic behavior. The choice between them is driven by other factors (e.g., which mapping fits a larger workflow).

![[Pasted image 20260525092323.png]]

## Checkerboard mapping

> [!tldr] Checkerboard
> Arrange processes in a $\sqrt{p} \times \sqrt{p}$ grid and give each one a square tile of $A$. The vectors live in the rightmost column of processes (the vector $x$ is on the right, so naturally the processes that span the square tiles on the right also contain the vector $x$).
> - each process owns a $\left(\sqrt{N/p} \times \sqrt{N/p}\right)$-submatrix
> 
> Four phases:
> (1) boundary column ships its $\vec{x}$ chunks to the diagonal
> (2) each diagonal process broadcasts its chunk down its column
> - so each row has all $x_0$, $x_1$, $x_2$ etc. to multiply it with the values it has (phase 3)
> - we need the same value $x_0$ along the whole column
>
> (3) every process multiplies its tile by the subvector it received
> - it receives a vector chunk of the $x$ vector (it is a small local matrix to vector multiplication and the result is also a vector)
> 
> (4) each row of processes reduces its partial sums back to the rightmost column to form $\vec{y}$.
> 
> More communication phases but each one is smaller and runs in parallel across rows or columns, so the total cost and scalability match the striped mappings: constant efficiency requires $\sqrt{N}/p \geq \text{const}$.

Processes form a virtual 2-D mesh $M(\sqrt{p}, \sqrt{p})$. Each process $P_{i,j}$ owns a $(\sqrt{N/p} \times \sqrt{N/p})$-submatrix of $A$. For the input/output vectors, the standard assumption used here is: vectors $\vec{x}$ and $\vec{y}$ are mapped to the last column of the virtual 2-D mesh (the rightmost column of processes). Other choices are possible; this is just the convention used in the analysis. Algorithm `CheckerBoardMVM`$(A, \vec{x}, \vec{y})$:

- Ph.1: For all $i = 0, \ldots, \sqrt{p}-1$ in parallel, boundary processor $P_{i, \sqrt{p}-1}$ sends its part of $\vec{x}$ to the diagonal processor $P_{i,i}$.
- Ph.2: For all $i = 0, \ldots, \sqrt{p}-1$ in parallel, $P_{i,i}$ broadcasts the received part of $\vec{x}$ within its column.
- Ph.3: For all $i, j = 0, \ldots, \sqrt{p}-1$ in parallel, $P_{i,j}$ multiplies its local submatrix of $A$ with its received subvector of $\vec{x}$.
- Ph.4: For all $i = 0, \ldots, \sqrt{p}-1$ in parallel, the processors $P_{i,*}$ in row $i$ perform a parallel reduction with root $P_{i, \sqrt{p}-1}$, summing the partial results into the final $\vec{y}$. Note that Phase 2 is not an inversion of a single broadcast - it is $\sqrt{p}$ independent column-wise broadcasts running in $\sqrt{p}$ disjoint column-sub-topologies in parallel. Similarly, Phase 4 consists of $\sqrt{p}$ independent row-wise reductions running in $\sqrt{p}$ disjoint row-sub-topologies in parallel.

![[Pasted image 20260525092349.png]]
## Time complexity and scalability of `CheckerBoardMVM`

Each process owns a $\left(\sqrt{N/p} \times \sqrt{N/p}\right)$-submatrix.

- Phase 3 (local arithmetic): $T_3 = k_3 \dfrac{N}{p}$ parallel arithmetic operations.
- Phase 1 complexity is of the same order (SF) or lower order (WH) than Phase 2 and can be ignored.
- Phase 4 has the same asymptotic complexity as Phase 2 (broadcast and reduction run on the same data sizes on the same topology; the small constant from the addition operations does not change the asymptotic).
- Phase 2 is an OAB of $\sqrt{N/p}$ numbers; its latency depends on the underlying topology and switching. Example: SF mesh $M(\sqrt{p}, \sqrt{p})$: $$ T_2 = k_2 \sqrt{p} \cdot \sqrt{\frac{N}{p}} = k_2 \sqrt{N}, $$ which is the same asymptotic order as in `RowWiseMVM`. Hence $$ E(N, p) \geq E_0 \quad \text{if} \quad \frac{\sqrt{N}}{p} \geq \text{const}. $$ The scalability is essentially the same as for the striped mappings on the same hardware: assuming identical local node performance and identical communication links, all three mappings give asymptotically equivalent total time complexity.

## Comparative summary

- Row-wise: AAB of $\vec{x}$ first, then local dot products. Output $\vec{y}$ mapped same as input $\vec{x}$.
- Column-wise: local partial products first, then $p$ simultaneous reductions to produce the output.
- Checkerboard: send $\vec{x}$ from boundary to diagonal, broadcast in columns, local submatrix-subvector multiply, reduce in rows.
  
On comparable underlying hardware (same topology of given size, same node performance, same link performance), all three have the same asymptotic time complexity: $$ T(N, p) \doteq k'_1 \sqrt{N} + k_2 \frac{N}{p}, $$
and the same scalability: constant efficiency requires $p \leq c \sqrt{N}$ for some constant $c$, equivalently a constant number of matrix rows per processor (or, for checkerboard, $\sqrt{N}/p \geq$ const).

## Potential exam questions

- State the dense MVM problem and define the matrix and vector dimensions used in the analysis. What does $N$ denote and why is the matrix written as $(\sqrt{N} \times \sqrt{N})$?
- Describe in detail the `RowWiseMVM` algorithm. What is the size of each vector block? Which collective communication operation is used in Phase 1, and what is the data layout of $\vec{y}$ at the end?
- Derive the time complexity $T_2(N, p)$ of Phase 2 of `RowWiseMVM`. Show that $T_2 = \Theta(N/p)$ corresponds to $\sqrt{N}/p$ dot products of length $\sqrt{N}$.
- For `RowWiseMVM` on a full-duplex 4-port SF $M(\sqrt{p}, \sqrt{p})$ with noncombining TADT-based AAB, derive $T_1(N, p)$ and explain why it simplifies to $\doteq k_1 \sqrt{N}/4$ asymptotically.
- For `RowWiseMVM` on a full-duplex 1-port SF $M(\sqrt{p}, \sqrt{p})$ with combining AAB by dimensions, derive $T_1(N, p)$ and show that it is also $\doteq k_1 \sqrt{N}$ asymptotically.
- Starting from $T(N, p) = k'_1 \sqrt{N} + k_2 N/p$, derive the isoefficiency condition $E(N, p) \geq E_0$ for `RowWiseMVM` and conclude what constraint this places on the relationship between $p$ and $N$. Explain why this implies constant efficiency with a constant number of matrix rows per processor.
- Describe `ColumnWiseMVM`. Why is the order of computation and communication reversed compared to row-wise? What is the structure of Phase 2 (how many simultaneous reductions, and on what data)?
- Explain why `RowWiseMVM` and `ColumnWiseMVM` have asymptotically the same complexity and scalability, despite the different communication patterns.
- Describe all four phases of `CheckerBoardMVM`. In particular, explain why Phase 2 is $\sqrt{p}$ independent column broadcasts rather than a single global broadcast.
- Why must the input vector $\vec{x}$ first be sent from the last column of processes to the diagonal in Phase 1 of `CheckerBoardMVM`?
- Derive the time complexity of Phase 3 of `CheckerBoardMVM`. Why does the complexity of Phase 1 not contribute asymptotically?
- For `CheckerBoardMVM` on an SF $M(\sqrt{p}, \sqrt{p})$ mesh, show that $T_2 = k_2 \sqrt{N}$ (where $T_2$ is the cost of Phase 2 OAB), and conclude that the total time complexity is of the same order as `RowWiseMVM`.
- State and justify the scalability condition for `CheckerBoardMVM`: under what relationship between $\sqrt{N}$ and $p$ is constant efficiency preserved?
- Compare the three mappings (row-wise, column-wise, checkerboard) in terms of: (a) total time complexity, (b) scalability, (c) the structure of communication, (d) the mapping of the output vector. Are there practical reasons to prefer one over the others despite their asymptotic equivalence?
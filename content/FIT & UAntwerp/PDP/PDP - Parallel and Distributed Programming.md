This note contains my own solutions to the exam questions, organized by the lecture. Sources: slides, lectures, my own explanations, AI.

Disclaimer: for the full context and correct understanding, the official slides are needed. Also, the list of the questions may differ each academic year. 

Lecture 1
- [[PDP - PRAM model and shared memory access conflict resolution]]
- [[PDP - APRAM model and implementation of synchronization barriers]]
- [[PDP - Parallel time, speedup, cost, efficiency and parallel performance optimality]]
- [[PDP - Amdahl's law, Gustafson's law, and Isoefficiency functions]]

Lecture 2
- [[PDP - OpenMP programming model, parallel regions, properties of variables]]
- [[PDP - OpenMP data parallelism (directive for), semantics, parameters]]
- [[PDP - OpenMP functional parallelism (directive task), semantics, parameters]]
- [[PDP - OpenMP Synchronization directives]]

Lecture 3
- no exam questions

Lecture 4
- [[PDP - OpenMP Classification of parallelizable programs, sources of inefficiency of multithreaded programs, false sharing and its elimination]]
- [[PDP - OpenMP Parallelization of histogram computation]]
- [[PDP - OpenMP Parallelization of polynom multiplication]]
- [[PDP - OpenMP Parallelization of dense matrix-matrix multiplication]]
- [[PDP - OpenMP Formats for storing sparse matrices and parallelization of sparsematrix-vector multiplication]]

Lecture 5
- [[PDP - OpenMP Basic ideas of parallel QuickSort and parallel partitioning]] 
- [[PDP - OpenMP Basic ideas of parallel 2-way MergeSort]] 
- [[PDP - OpenMP Basic ideas of parallel p-way MergeSort]] 

Lecture 6
- [[PDP - OpenMP+MPI cooperation of processes and threads]] 
- [[PDP - MPI blocking communication operations and their communication modes, state objects]] 
- [[PDP - MPI nonblocking communication operations and tests of their completion, state objects]] 
- [[PDP - MPI probing of messages]] 
- [[PDP - MPI return values of functions and error handling]] 
- [[PDP - MPI implementations of cyclic shift permutation]]

Lecture 7
- [[PDP - Basic graph properties of interconnection networks (sparse or dense topology, hierarchically recursive topology, distances, regularity, connectivity, bisection width, bipartiteness)]] 
- [[PDP - Cartesian product of graphs, vertex symmetry]]
- [[PDP - n-dimensional hypercube - definition, properties, routing]] 
- [[PDP - n-dimensional mesh - definition, properties, routing]] 
- [[PDP - n-dimensional torus - definition, properties, routing]] 
- [[PDP - Sparse hypercubic networks and fat trees - definition, properties, routing]] 
- [[PDP - Clos topologies - definitions, variants, properties]] 
- [[PDP - Dragonfly topology - definition, properties]]

Lecture 8
- [[PDP - Quasiisometric topologies - meshes-tori, ordinary-wrapped butterflies]]
- [[PDP - Embedding of the hypercube into lower-dimensional meshes]]

Lecture 9 (no summary at the top of all exam questions)
- [[PDP - Switching technology in parallel computers - Store-and-Forward and wormhole - description, communication latency]] 
- [[PDP - OAB in all-port and 1-port SF networks - lower bounds, algorithms and their latencies]]
- [[PDP - OAB in 1-port WH networks - lower bounds, algorithms and their latencies]]
- [[PDP - OAB in all-port WH 2-D tori - lower bounds, the dilated diagonal algorithm and its latency]]
- [[PDP - Multicast in 1-port WH 2-D meshes - lower bounds, optimal algorithm, and its latency]]
- [[PDP - Combining OAS - lower bounds, algorithms, and their latencies]]
- [[PDP - Noncombining AAB and AAG - lower bounds, algorithms, and their latencies. Timearc disjoint trees and edge-disjoint hamiltonian circuits]]
- [[PDP - AAS - communication latency lower bounds]]

Lecture 10
- [[PDP - Parallel prefix sum (PPS) - definition, implementation on PRAM, APRAM, and various topologies and their complexities, scalability, MPI function]]
- [[PDP - PPS application - packing problem and parallel RadixSort]]  
- [[PDP - PPS application - carry-look-ahead adder]]  
- [[PDP - PPS application - tridiagonal system of linear equations]]  
- [[PDP - Segmented parallel prefix sum - definition, implementations on PRAM and various topologies, and their complexities, scalability, MPI functions]]
- [[PDP - Out-of-place parallel QuickSort based on segmented parallel prefix sum]]  

Lecture 11
- no exam questions

Lecture 12
- [[PDP - MPI implementation of distributed generation of random permutation of sequence 1...n]] 
- [[PDP - Multiplication of a dense matrix with a vector - row-wise, column-wise, checkerboard mapping, scalability]] 
- [[PDP - Cannon algorithm of distributed multiplication of dense matrices, correctness, properties]] 
- [[PDP - MPI implementation of the Cannon algorithm for dense matrix-matrix multiplication]] 
- [[PDP - MPI implementation of the Power Method with arbitrary and row-wise matrix mapping]] 
- [[PDP - MPI implementation of the Power Method with checkerboard matrix mapping]] 
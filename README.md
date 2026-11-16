# Lab 09: Exploring D&C Algorithms

**Instructions:** Work in small groups to complete the following exercises. Try sketching out your work on the whiteboards or scratch paper. Everyone should submit their own answers to the exercises, but you are encouraged to discuss your reasoning with your group members.

Name(s): Deniz Akbas

---

## Exercise 1: Recurrence Extraction

Consider the following divide-and-conquer algorithm, which takes an array $A$ of $n$ numbers as input and returns a single number. 

```text
Procedure Mystery(A, n)
    if n <= 1:
        return A[1]                              // Base case
        
    k = floor(n / 3)                             // Find the one-third point
    
    left = Mystery(A[1..k], k)                   // Recur on the left third
    right = Mystery(A[k+1..n], n - k)            // Recur on the rest
    
    total = 0 
    for i = 1 to n:
        total = total + (A[i] * left * right)    // Combine results
        
    return total
```

### Part A: The Recurrence Relation
Let $T(n)$ be the running time of `Mystery` on an input of size $n$. Write down a recurrence relation for the worst-case running time $T(n)$. You may assume $n$ is a power of 3 for simplicity. *(Note: You do not need to solve the recurrence relation yet, just formulate it).*

Each call splits the input into a part of size k = n/3 and a part of size n − k = 2n/3, then recurs on both. The combine step is a loop over all n elements, which does Θ(n) work. The base case is constant time.


### Part B: Visualizing Algorithm Behavior
Without solving the recurrence, look closely at the recursive calls and the combine step. If you were to draw the recursion tree for this algorithm, what would it look like? Is the tree balanced or skewed? 

The recursion tree is skewed (unbalanced). Every node has two children, but the left child gets 1/3 of the input and the right child gets 2/3. The left branches shrink by a factor of 3 each level, so the shallowest path (always going left) has depth log₃ n. The right branches shrink only by a factor of 3/2, so the deepest path (always going right) has depth log₍₃/₂₎ n. The tree is therefore not balanced, and its leaves sit at different depths. Each full level still does about cn total work, because the subproblem sizes at any level add up to n.

---

## Exercise 2: Unrolling the Recursion Tree

Suppose you have developed a new divide-and-conquer algorithm. You analyze your pseudocode and determine that its worst-case running time $T(n)$ satisfies the following recurrence relation:

$$T(n) \leq 3T\left(\frac{n}{2}\right) + cn^2 \quad \text{for } n > 1$$
$$T(1) \leq c$$

where $c$ is a positive constant. 

### Part C: Recursion Tree Analysis

Analyze the running time of the algorithm by unrolling the recursion tree. Specifically, answer the following questions:

1. **Root Work:** Find the amount of work done at the root level (level $0$) of the recursion tree.
- One problem of size n, so the work is cn².
2. **First Levels:** Find the amount of work done at level $1$ and level $2$ of the recursion tree.
Level 1 has 3 subproblems of size n/2, each costing c(n/2)² = cn²/4, for a total of 3cn²/4.
Level 2 has 9 subproblems of size n/4, each costing c(n/4)² = cn²/16, for a total of 9cn²/16.
3. **Tree Height:** Find the height of the recursion tree (at what level does the input size reach $1$?).
- At level i the input size is n/2ⁱ. This reaches 1 when 2ⁱ = n, so i = log₂ n. The tree has log₂ n + 1 levels.

**Bonus!** Find a general formula for the total work done at level $i$ of the recursion tree.
Level i has 3ⁱ subproblems, each of size n/2ⁱ, so the total work at level i is 3ⁱ · c(n/2ⁱ)² = cn² · (3/4)ⁱ


---

## Exercise 3: Reflection

1. During lecture last week, we looked at two different divide-and-conquer algorithms. What were these algorithms and what problems did they solve?
- Merge sort sorts an array of numbers. It splits the array in half, recursively sorts each half, and merges the two sorted halves in linear time. Its running time is Θ(n log n).
- Nearest neighbors (closest pair of points in 2D) takes n points in the plane and finds the pair with the smallest distance between them. It splits the points in half, recursively finds the closest pair on each side, then checks the strip near the dividing line for a closer pair that crosses the split. This gives O(n log n), compared with Θ(n²) for checking every pair.



2. How do the algorithms you analyzed in this lab compare to the ones we discussed in lecture? Specifically, each algorithm differs from the two we discussed in lecture in one important way. For each of the algorithms above, what is that difference?
- Mystery (Exercise 1): It splits the input unevenly, into pieces of size n/3 and 2n/3, instead of equal halves. The recursion tree is therefore skewed rather than balanced, and the recurrence T(n) = T(n/3) + T(2n/3) + cn can’t be solved with the usual equal-split pattern.
- The Exercise 2 algorithm: It makes three recursive calls (3T(n/2)) instead of two. Its combine step is also quadratic (cn²) instead of linear. Because of the extra branching, the number of subproblems grows faster at each level, but the per-level work shrinks by a factor of 3/4, so the root dominates the total cost.
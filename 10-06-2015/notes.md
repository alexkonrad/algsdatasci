# October 6, 2015

## Linear least squares fitting

A foundational problem in statistics: find a line of *best fit* through some data points

### Input: a set *P* of *n* data points *(x1,y1), (x2,y2),...,(xn,yn)*; we assume *x1 < x2 < ... < xn*

### Output: the line *L* defined as *y = ax + b* that *minimizes* the error

`err(L,P) = sum from i=1 to n of (yi - axi - b)^2`

Given a set *P* of data points, we can use calculus to show that the line *L* given by *y = ax + b* that minimizes `err(L,P)` satisfies.

## What if the data changes direction more than once?

Any single line would have large error.

### Idea 1: hardcode number of lines to 2 (or some *fixed m*).

### Idea 2: pass an arbitrary set of lines through the points and seek the set of lines that minimizes the error.

  - Trivial solution: have a different line pass through each pair of consecutive points in *P*.

### Idea 3: fit the points well, using as few lines as possible.

  - tradeoff between complexity and error of the model

## Formalizing the problem

### Input: data set *P = {p1, ..., pn}* of points on the plane.

  - A **segment** *S={pi, pi+1, ..., pj}* is a contiguous subset of the input.

  - Let Sigma be a partition of *P* into *m_Sigma* segments *S_1, S_2, ..., S_m_Sigma*. For every segment *S_k*, use (2), (3), (4) to compute a line *L_k* that minimizes `err(L_k, S_k)`.

  - Let *C > 0* be a fixed multiplier. The **cost** of the partition is

```
sum over S_k in Sigma of err(L_k, S_k) + m_Sigma*C
```

## Segmented least squares

This problem is an instance of change detection in data mining and statistics.

**Input**: A set *P* of *n* data points *p_i = (x_i, y_i)* as before.

**Output**: A segmentation *Sigma' { S_1, S_2, ..., S_m_Sigma }* of *P* whose **cost** is minimum.

## A brute force approach

We can find the optimal segmentation (that is, the one incurring the minimum penalty) by exhaustive search.

  - Enumerate every possible segmentation and compute its penalty.

  - Output the one that incurs the minimum penalty

*O(2^n)* partitions

### Example

```
1 segment = 1 segmentation
2 segments = n - 1 choose 1
3 segments = n - 1 choose 2
.
.
.
n segements = n - 1 choose n - 1 (1)
```

## Running time

```
T(n) = time to compute OPT(n)
T(n) >= T(n-1) + T(n-2)
  => O(2^(n/2))
```

## A crucial observation regarding the last data point

Consider the last point *p_n* in the data set.

  - *p_n* belongs to a single segment in the **optimal** partition.

  - That segement starts at an earlier point *p_i*, for some *1 <= i <= n*.

This suggests a recursive solution: if we knew where the last segment starts, then we could remove it and recursively solve the problem on the remaining points *{p_1, ..., p_i-1}*.

## A recursive approach

  - Let `OPT(j)` denote the cost of the optimal segmentation for points *p_1, ..., p_j*.

  - Then, if the last segment of the optimal segmentation is *{p_i, ..., p_n}* the cost of the optimal solution is `OPT(n) = err(L, {p_i upto p_n}) + C + OPT(i-1)`.

  - But we don't know where the last segment starts. How do we find the point *p_i*?

  - Set

```
OPT(n) = min over 1 <= i <= n of Error(L, {p_i upto p_n} + C + OPT(i-1))
```

## A recurrence for the optimal solution

**Notation**: let `e_i,j = err(L, {p_i upto p_j}) for 1 <= i <= j <= n`.

Then

```
OPT(n) = min over 1 <= i <= n of e_i,n + C + OPT(i - 1)
```

If we apply the above expression recursively to remove the last segment, we obtain the recurrence

```
OPT(j) = min over 1 <= i <= j of e_i,j + C + OPT(i - 1)
```

### Remark

  1. We can precompute and store all *e_i,j* in *O(n^3)* time.

  2. The natural recursive algorithm rising from recurrence is **not** efficient.

`T(n) = T(n-1) + T(n-2)`

Same as for the recursive Fibonacci solution

If you draw the recurrence tree, you'll see that values like `T(n-1)`, `T(n-2)`, etc., are reused and show up multiple times. This indicates that we can use dynamic programming to find a solution.

## Are we really that far from an efficient solution?

Recall Fibonacci: exponential recursive algorithm, polynomial iterative solution.

### How?

  1. **Overlapping subproblems**: spectacular redundancy in computations of recursion tree

  2. **Easy-to-compute recurrence** for combining solutions to the smaller subproblems into a solution to a larger subproblem in *O(n)* time (once smaller subproblems have been solved).

  3. **Iterative, bottom-up computations**: we computed the subproblems from smallest *(F_0, F_1)* to largest *(F_n)*, iteratively.

  4. **Smaller number of subproblems**: only solved *n - 1* subproblems.

## A dynamic programming approach

```
OPT(j) = min of 1 <= i <= j of e_i,j + C + OPT(i - 1)
```

  - The optimal solution to the subproblem on *p_1, ..., p_j* contains optimal solutions to smaller subproblems.

  - The recurrence provides an *ordering* of subproblems from smaller to larger, with the subproblem of size 0 being the smallest and the subproblem of size *n* the largest.

  - There are *n + 1* subproblems in total. Solving the *j*-th subproblem requires `Theta(j) = O(n)` time.

  - The overall running time is `O(n^2)`

  - Boundary conditions: `OPT(0) = 0`

  - Segment *p_k, ..., p_j* appears in the optimal solution only if the minimum in the expression above is achieved for *i = k*.

## An iterative algorithm for segmented least squares

Let *M* be an array of *n* entries. *M[i]* stores the cost of the optimal segmentation of the first *i* data points.

```
SegmentedLS(n, P)
  M[0] = 0
  for all pairs i ≤ j do
  Computeei,j forsegmentpi,...,pj using(2),(3),(4) end for
  for j = 1 to n do
  M[j]= min{ei,j +C+M[i−1]}
  1≤i≤j end for
  Return M[n]
```

## Reconstructing an optimal solution

  - Suppose we want the optimal solution in addition to its value, that is, the actual segmentation that achieves the minimum cost *M[n]*.

  - We can trace back through the dynamic programming array *M* to compute the optimal segmentation.

Initial call: `OPTSegmentation(n)`

```
OPTSegmentation(j )
  if (j == 0) then return else
  Find 1 ≤ i ≤ j such that M[j] = ei,j + C + M[i − 1] OPTSegmentation(i − 1)
  Output segment {pi,...,pj}
  end if
```
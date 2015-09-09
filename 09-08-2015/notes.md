# September 8, 2015

[Slides](http://algsdatasci.wikischolars.columbia.edu/file/view/slides9-8.pdf/558609823/slides9-8.pdf)

### Example: Insertion Sort
#### Definition
Input: a list `A` of integers `x_1 ..., x_n`
Output: a list `A'` of integers `x_1', ..., x_n'`

#### Example: 
Input: `n = 6, A = {9, 3, 2, 6, 8, 5}`
Output: `A = {2, 3, 4, 5, 6, 8, 9}`

#### Steps:
  1. Start with a trivially sorted subarray of size 1 (the first element of `A`)
  2. Insert the next element of `A` by inserting the next element into its correct location
    * Compare `key` with every `x` of the subarray starting from the right
      - If `x > key`, move `x` one position to the right
      - else `x < key`: insert `key` after `x`

#### Correctness
Observation: after loop `i`, the subarray `A[1,i]` is sorted

##### Base case
  
`i = 1` is trivial

##### Induction Hypothesis

Assume the statement is true for some `1 <= i < n`

##### Inductive Step

Loop `i + 1`:
  
  1. element `key = A[i + 1]` is inserted into `A[1, i]`, which is sorted (by the induction hypothesis).
  2. Since key is inserted after the first element `A[l]` for `1 ≤ l ≤ i` such that `key ≥ A[l]`, and all elements in `A[l + 1, j]` are pushed one position to the right with their order preserved, the statement is true for `i + 1`.

#### Running time
Number of primitive computational steps

Count how many times each line is executed:
  
```
for i = 2 to n do                 # executed n times
  key = A[i]                      # n-1 times
  j = i − 1                       # n-1 times
    while j > 0 and A[j] > key do 
```

because we can't say *a priori* how many times line 4 is executed,

`for 2 <= i <= n, let ti be the number of times line 4 is executed`

so line 4 is executed `t1 + t2 + ... + tn` times

```
  A[j + 1] = A[j]
  j = j − 1
end while
```

line 5 and 6 are executed `t1 + t2 + ... + tn` times

```
A[j+1] = key                      # executed n-1 times
end for
```

##### Best case: sorted array

`5n - 4`

linear running time

##### Worst case

`(3n^2)/2 + 7n/2 - 4`

Reverse-sorted array: quadratic

For proof, consider:

`1 + 2 + ...  + n = (n * (n + 1)) / 2`

##### Efficiency
** Question: ** is it efficient?

To answer, compare to **brute force** solution:

  1. at each step, generate a new permutation of `n` integers
  2. if this permutation is sorted, stop and output the permutation

To do this we generate `n!` permutations (see Stirling's approximation formula)

###### Definition 1: its worst-case performance is better than that of brute-force search

** Caveat: ** ignores the *scaling properties* of the algorithm

As the input size grows by a constant factor, we want running time `T(n)` to increase by a constant factor

###### Definition 2: it has a polynomial running time

** Caveat: ** what about large constants in front of the leading term or large exponents

However,

  - *small degree polynomial* running times exist for most problems that can be solved in polynomial time
  - conversely, problems for which no polynomial-time algoirthm is known tend to be very hard in practice
  - so we can distinguish between * easy * and * hard * problems

** Remark: ** even low degree polynomials might be too slow

So insertion sort is efficient.

*Are we done with sorting?*

  1. Can we do better?
  2. What is better?
    - e.g., is *T(n) = n^2 + n - 4* worth aiming for?

Exact characterizations

  - are *too detailed*
  - do not reveal similarities between running times in an immediate way as `n` grows large
  - are often *meaningless*: pseudocode steps will *expand* by a constant factor that depends on the hardware
# September 10, 2015

## Asymptotic analysis

Allow us to compare the *rate of growth* of different running times as the input size *n* grows

  - Express the running time of the number of primitive steps
  - The number of primitive steps is itself a function of n

Hence the running time is a function of size *n*

### Definition 1 (*O*)

We say that `T(n) = O(f(n))` if there exists constants `c > 0` and `n_0 >= 0` such that for all `n >= n_0`, we have `T(n) <= c * f(n)`

#### Examples

Suppose `T(n) = 2n^2 + 1`, `f(n) = n^2`

Show `2n^2 + 1 = O(n^2)`:

So we need a constant *c* and a constant *n_0* such that for all `n > n_0`, `2n^2 + 1 <= cn^2`

**Solution**: `n_0 = 1`, `c = 3`

In general, for `an^2 + b`, `c = a + b` and `n_0 = 1`

For `an^3 + b`, `c = a + b` and `n_0 = 1`

**Remark**: `T(n)` and `cf(n)` grow at the same rate

### Definition 2 (*Omega*)

We say that `T(n) = Omega(f(n))` if there exists constants `c > 0` and `n_0 >= 0` such that for all `n >= n_0`, we have `T(n) >= c*f(n)`

For `an^2 + b`, we need `c` and `n_0` such that `an^2 >= cn^2` for all `n` > `n_0`

**Solution**: `n_0 = 1`, `c = a`

### Definition 3 (*Theta*)

We that `T(n) = Theta(f(n)) if there exist constants `c_1, c_2 > 0` and `n_0 >= 0` such that for all `n >= n_0` we have `c_1 * f(n) <= T(n) <= c_2 * f(n)`

#### Equivalent definition

`T(n) = Theta(f(n))` if `T(n) = O(f(n))` and `T(n) = Omega(f(n))`

#### Example

`T(n) = n * log(n) + n` and `f(n) = n * log(n)`

**Show**: `T(n) = n * log(n) + n = Theta(n * log(n))`

Find `c_1, c_2, n_0` such that `c_1 * (n * log(n)) <= n * log(n) <= c_2 * (n * log(n))`

**Solution**: `c_1 = 1, c_2 = 2, n_0 = 2`

**NB**: All logarithms are base 2 unless otherwise noted

### Definition 4 (*o*)

Asymptotic upper bounds that are *not tight*

We say that `T(n) = o(f(n))` if for any constant `c > 0` there exists a constant `n_0 >= 0` such that for all `n >= n_0`, we have `T(n) < c * f(n)`

Intuitively, `T(n)` becomes insignificant relative to `f(n)` as `n -> Infinity`

**Proof**: show that `lim T(n)/f(n) -> 0` as `n -> Infinity`

#### Examples

`T(n) = an^2 + n`, `a, b > 0` constants and `f(n) = n^3`

`T(n) = n * log(n)`, `a,b,d > 0` constants and `f(n) = n^2`

**Proof**: `lim n * log(n) / n ^ 2` equals `lim log (n) / n`, which equals `1/n` by L'Hopital's rule

### Definition 5 (w)

We say that `T(n) = w(f(n))` if for any constant `c > 0` there exists `n_0 >= 0` such that for all `n >= n_0`, we have `T(n) > c * f(n)`

`T(n) = w(f(n))` implies that `lim T(n)/f(n) = Infinity` as `n -> Infinity` if the limit exists. Then `f(n) = o(T(n))`

#### Examples

`T(n) = n^2` and `f(n) = n * log(n)`
`T(n) = 2^n` and `f(n) = n^2`

`lim 2^n/n^5` is equal to `lim 2^n * ln(2) / 5n^4` ... Infinity

### Basic rules for omitting lower order terms from functions

  1. Ignore *multiplicative* factors: e.g., `10n^3` becomes `n^3`
  2. `n^a` dominates `n^b` if `a > b`, e.g., `n^2` dominates `n`
  3. Exponentials dominate polynomials: e.g., `2^n` dominates `n^4`
  4. Polynomials dominate logarithms: e.g., `n` dominates `log^3(n)` for large enough n

`log(n) < n < n*log(n) < n^2 < 2^n < 3^n < n^n`

### Properties of asymptotic growth rates

#### Transitivity

  1. If `f = O(g)` and  `g = O(h)`, then `f = O(h)`
  2. If `f = Omega(g)` and `g = Omega(h)`, then `f = Omega(h)`
  3. If `f = Theta(g)` and `g = Theta(h)`, then `f = Theta(h)`

#### Sums of (up to a constant number of) functions

  1. If `f = O(h)` and `g = O(h)` then `f + g = O(h)`
  2. Let `k` be a fixed constant, and let `f_1, f_2, ... f_k, h` be functions such that for all `i`, `f_i = O(h)`. Then `f_1 + f_2 + ... + f_k = O(h)`.

#### Transpose symmetry

  1. `f = O(g)` iff `g = Omega(f)`
  2. `f = o(g)` iff `g = w(f)`

## The Divide and Conquer Principle

**Divide** the problem into a number of subproblems that are smaller instances of the same problem.

**Conquer** the subproblems by solving them recursively.

**Combine** the solutions to the subproblems into the solution for the original problem.

### Mergesort

#### Steps

  1. Divide the input array into two lists of equal size

  2. Sort each list recursively

  3. Merge the two sorted lists and output the sorted array

#### Pseudocode

```
Mergesort(A, left, right)
  if right == left then return
  end if
  middle = left + floor((right - left)/2)
  Mergesort(A, left, middle)
  Mergesort(A, middle + 1, right)
  Merge(A, left, middle, right)
```

```
Merge(A, left, right, mid)
  L = A[left, mid]
  R = A[mid + 1, right]
  Maintain two pointers CurrentL, CurrentR initialized to point to the first element of L, R
  while both lists are nonempty do
    Let x, y be the elements pointed to by CurrentL, CurrentR
    Compare x, y and append the samller to the output
    Advance the pointer in the list with the smaller of x, y
  end while
  Append the remainder of the non-empty list to the output
```

**Remark**: The output is stored directly in `A[left, right]` thus the subarray `A[left, right]` is sorted after `Merge(A, left, right, mid)`

**Exercises**:

  1. write detailed pseudocode or python code for `Merge`

  2. write a recursive `Merge`

```python
def merge(arr, left, right, mid)
  out = []

  l = arr[left, mid]
  r = arr[mid + 1, right]

  m = 0
  n = 0

  while l.length > 0 and r.length > 0
    x = l[m]
    y = r[n]

    x < y ? out.push(x) : out.push(y)
  end
end
```

```python
def merge
end
```

#### Analysis of Merge

##### Correctness

The smaller number in the input is `L[1]` or `R[1]` and it will be the first number in the output. The rest of the output is just the list obtained by `Merge(L, R)` after deleting the smallest element.

##### Running time
```
Merge(A, left, right, mid)
```

The following lines can be expanded into `for i=0 to n/2 + 1 L[i + l] = A[left + i]`
So they are O(n/2)

```
  L = A[left, mid]
  R = A[mid + 1, right]
```
The following line takes two instructions

```
  Maintain two pointers CurrentL, CurrentR initialized to point to the first element of L, R
```

The following instruction can be executed at most `n` times:

```
  while both lists are nonempty do
```

```
    Let x, y be the elements pointed to by CurrentL, CurrentR
```

We have about `n` comparisons, each of which takes constant time.

Copying a value to the output takes constant time:

```
    Compare x, y and append the samller to the output
```

```
    Advance the pointer in the list with the smaller of x, y
```

Takes constant time.

```
  end while
```

So the entire loop takes *n* * constant time. So the loop is `O(n)`

The following line could be executed up to `n` times:

```
  Append the remainder of the non-empty list to the output
```

So it is `O(n)`

So `Merge` is `O(n)`

Memory is `O(n)`

**Example**: Run Mergesort with input {1,7,4,3,5,8,6,2}

##### Space

extra `Θ(n)` space to store `L`, `R` (the sorted output is stored directly in `A`).

#### Analysis of Merge

##### Correctness

**TODO**: Go over this

##### Running Time

`T(n)` is the running time of Mergesort for input of size n

Follow along line-by-line with the pseudocode:

`T(n) = c + c + T(n/2) + T(n/2) + (c' * n)`

so

`T(n) = 2T(n/2) + cn`

This doesn't tell us the running time of merge sort.

It tells us the running time of a larger input in terms of a smaller input.

It is a *recurrence relation*:
  
  - an **inequality** or **equation** bounds `T(n)` in terms of an expression involving `T(m)` for `m < n`

  - a base case generally says that `T(n)` is constant for small constant `n`

**Remark**: We ignore floor and ceiling notations

**Remark**: A recurrence does *not* provide an asymptotic bound for `T(n)`: to this end, we must *solve* the recurrence.

##### Space
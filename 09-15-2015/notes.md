# September 15, 2015

## Solving recurrences

### Mergesort

```
Mergesort(A,left,right)
  if right == left then return
  end if
  middle = left + floor((right - left)/2)
  Mergesort(A,left,middle)
  Mergesort(A,middle+1,right)
  Merge(A,left,middle,right)
```

#### Recursion tree

```
T(n) = 2T(n/2) + cn
```

Level 1: 2 subproblems, each have size n/2

```
T(n/2) = 2(n/4)
```

Level 2: 4 subproblems, each have size n/4

```
T(n) = 4T(n/4)
```

Level i: `2^i` subproblems, each have size `n/(2^i)`

`2^i * n/2^i = cn`

Now we sum up the work for every level

At level d, the size of each subproblem will be `n/2^d = 1 => n = 2^d => d = log(n)`

`T(n) = sum(c(n)) from i = 0 to log(n) = O(n*log(n))`

##### Summations

```
a = b^k => 

T(n) = sum(cn^k * (a/b^k)^i) from i = 0 to logb(n)

     = cn^k * sum((a/b^k)^i) from i = 0 to logb(n)

     = O(n^k*log2(n)) (given that logb(n) = log2(n)/log2(b))

```

Exercise: do the other two cases

```
a < b^k =>

T(n) = sum(cn^k * (a/b^k)^i) from i = 0 to logb(n)

     = cn^k * sum((a/b^k)^i) from i = 0 to logb(n)

```

##### Mergesort

```
T(n) = 2T(n/2) + cn

a = 2, b = 2, k = 1 =>

T(n) = O(n*log(n))
```

### Binary Search

**Input**: *sorted* list `A` of `n` integers, integer `x`

**Output**:

  - index `j` s.t. `1 <= j <= n` and `A[j] = x`, or

  - no if `x` is not in `A`

**Example**: `A = {0,2,3,5,6,7,9,11,13}, n = 9, x = 7`

#### Steps

First, probe the middle entry. Let `mid = ceil(n/2)`

#### Pseudocode

#### Running time

```
T(n) = T(n/2) + c

T(n) = aT(n/b) + cn^k

a = 1, b = 2, k = 0 => a = b^k

  => T(n) = O(log(n))
```

#### Concluding Remarks

  1. The right data structure can improve the running time of the algorithm significantly

    - What if we used a *linked list* to store the input?

    - Arrays allow for *random access* of their elements: given an index, we can read any entry in an array in time `O(1)` (constant time)

  2. In general, we obtain running time `O(log(n))` when the algorithm does a *constant amount of work* to throw away a *constant fraction* of the input


### Integer multiplication

  - How do we multiply two integers x and y?

  - Elementary school method: compute a partial product by multiplying every digit of `y` separately with `x` and then add up all the partial products.

  - Remark: this method works the same in base 10 or base 2

The “grade school method” is `O(n^2)`

#### Karatsuba algorithm

Rewrite

```
1014 = 10 * 10^2 + 14

80136 = 80 * 10^3 + 136
```

For n-digit decimal numbers x,y such that

```
x = x_n-1, x_n-2, ... x_0
y = y_n-1, y_n-2, ... y_0
```

Rewrite each number as the sum of `n/2` high-order digits and the `n/2` low-order digits.

```
x = x_h * 10^(n/2) + x_l
y = y_h * 10^(n/2) + y_l
```

Where each of the `x_h, x_l, y_h, y_l` is an `n/2`-digit number

##### Running time

```
T(n) = time to multiply 2 n-digit integers

T(n) = 4(n/2) + cn

a = 4, b = 2, k = 1 => a > b^k

  => T(n) = O(n^(logb(a))) = O(n^2)
```

No improvement. It will get better if we can get rid of one multiplication:

```
b = 3 => T(n) = O(n^logb3)
```

c.f. Gauss' observation (slides):

```
(x_h + x_l)(y_h + y_l) - x_h*y_h - x_l*y_l = x_h*y_l + x_l*y_h
```

Therefore,

```
T(n) <= 3T(n/2) + cn = O(n^(log2(3))) = O(n^1.59)
```

#### 
# Triangle Problem — Algorithm Project

## 1. Problem Description

We are given a zero-indexed array `A` of `N` integers. We need to find out if there exists three indices `(P, Q, R)` with `0 ≤ P < Q < R < N` such that the three numbers `A[P]`, `A[Q]`, `A[R]` can form the sides of a triangle.

Three numbers can form a triangle if **every two of them added together are bigger than the third one**. That means:

1. `A[P] + A[Q] > A[R]`
2. `A[Q] + A[R] > A[P]`
3. `A[R] + A[P] > A[Q]`

The function returns:
- `1` if such a triplet exists
- `0` if it does not exist

### Examples

**Example 1:**
- Input: `[10, 50, 1]`
- Output: `0`
- Why? The only triplet is `(10, 50, 1)`. But `10 + 1 = 11`, which is not bigger than `50`. So they cannot form a triangle.

**Example 2:**
- Input: `[10, 2, 5, 1, 8, 20]`
- Output: `1`
- Why? Take the triplet at indices `(0, 2, 4)` → values `(10, 5, 8)`.
  Check: `10+5=15 > 8` ✔, `5+8=13 > 10` ✔, `8+10=18 > 5` ✔. So they form a triangle.

---

## 2. First Algorithm — Sorting Approach (Non-Recursive)

### The Idea

If we sort the array from smallest to largest, then for any three numbers `a ≤ b ≤ c`:
- `b + c > a` is **always true** (because `b` and `c` are at least as big as `a`)
- `a + c > b` is **always true** (because `c` is at least as big as `b`)
- So the **only condition we really need to check** is `a + b > c`


So the algorithm is:
1. Sort the array using **bubble sort**
2. Look at every group of three consecutive numbers
3. If any group satisfies `A[i] + A[i+1] > A[i+2]`, return `1`
4. If none does, return `0`

### How Bubble Sort Works

Bubble sort works by repeatedly walking through the array and swapping any two neighbors that are in the wrong order:

1. Compare `A[0]` and `A[1]`. If `A[0] > A[1]`, swap them.
2. Compare `A[1]` and `A[2]`. If out of order, swap.
3. Keep going. After one full pass, the **largest element has "bubbled up"** to the end.
4. Repeat the pass on the remaining unsorted part of the array.

After `N − 1` passes, the whole array is sorted.

### Pseudocode

```
Algorithm Bubble-Sort(A):
    N = length(A)
    for i from 0 to N - 2:
        for j from 0 to N - 2 - i:
            if A[j] > A[j+1]:
                swap A[j] and A[j+1]


Algorithm hasTriangle(A):
    N = length(A)
    if N < 3:
        return 0

    Bubble-Sort(A)

    for i from 0 to N - 3:
        if A[i] + A[i+1] > A[i+2]:
            return 1

    return 0
```

### Implementation (Python)

```python
def bubble_sort(A):
    N = len(A)
    for i in range(N - 1):
        for j in range(N - 1 - i):
            if A[j] > A[j + 1]:
                A[j], A[j + 1] = A[j + 1], A[j]


def has_triangle_sorting(A):
    N = len(A)
    if N < 3:
        return 0

    A = list(A)
    bubble_sort(A)

    for i in range(N - 2):
        if A[i] + A[i + 1] > A[i + 2]:
            return 1

    return 0


print(has_triangle_sorting([10, 50, 1]))
print(has_triangle_sorting([10, 2, 5, 1, 8, 20]))
```

### Analysis & Complexity (Steps)

Let's count what each step costs, where `N` is the size of the array.

| Step | What it does | Cost |
|------|--------------|------|
| 1 | Check if `N < 3` | `O(1)` |
| 2 | Bubble sort outer loop runs `N − 1` times | `N` |
| 3 | Bubble sort inner loop runs up to `N − 1` times | `N` |
| 4 | Inside the inner loop: one comparison + maybe one swap | `O(1)` |
| 5 | Total work in bubble sort = `(N−1) + (N−2) + ... + 1 = N(N−1)/2` | `O(N²)` |
| 6 | Loop through `N − 2` triplets | `O(N)` iterations |
| 7 | Inside the loop: one addition + one comparison | `O(1)` per iteration |

**Total time complexity:**

```
O(1)  +  O(N²)  +  O(N) × O(1)
   =  O(N²)  +  O(N)
   =  O(N²)
```

The bubble sort step is the most expensive, so it dominates.


---

## 3. Second Algorithm — Brute Force (Recursive)

### The Idea

Just try **every possible triplet** `(P, Q, R)` and check the triangle conditions on each one.

Normally, this would be three nested `for` loops:
- Outer loop picks `P`
- Middle loop picks `Q` (with `Q > P`)
- Inner loop picks `R` (with `R > Q`)

To make it recursive, we **replace each loop with a recursive function**:
- `loop_P` plays the role of the outer loop
- `loop_Q` plays the role of the middle loop
- `loop_R` plays the role of the inner loop

Each one calls the next, and each one calls itself to advance its own index by one.

### Pseudocode

```
function isTriangle(A, P, Q, R):
    return  A[P] + A[Q] > A[R]
        AND A[Q] + A[R] > A[P]
        AND A[R] + A[P] > A[Q]


function loop_R(A, P, Q, R):
    if R >= length(A):
        return 0                  
    if isTriangle(A, P, Q, R):
        return 1                       
    return loop_R(A, P, Q, R + 1)      


function loop_Q(A, P, Q):
    if Q >= length(A) - 1:
        return 0                      
    if loop_R(A, P, Q, Q + 1) == 1:
        return 1
    return loop_Q(A, P, Q + 1)    


function loop_P(A, P):
    if P > length(A) - 3:
        return 0                    
    if loop_Q(A, P, P + 1) == 1:
        return 1
    return loop_P(A, P + 1)       


function hasTriangle(A):
    if length(A) < 3:
        return 0
    return loop_P(A, 0)
```

### Implementation (Python)

```python
def is_triangle(A, P, Q, R):
    return (A[P] + A[Q] > A[R] and
            A[Q] + A[R] > A[P] and
            A[R] + A[P] > A[Q])


def loop_R(A, P, Q, R):
    if R >= len(A):
        return 0
    if is_triangle(A, P, Q, R):
        return 1
    return loop_R(A, P, Q, R + 1)


def loop_Q(A, P, Q):
    if Q >= len(A) - 1:
        return 0
    if loop_R(A, P, Q, Q + 1) == 1:
        return 1
    return loop_Q(A, P, Q + 1)


def loop_P(A, P):
    if P > len(A) - 3:
        return 0
    if loop_Q(A, P, P + 1) == 1:
        return 1
    return loop_P(A, P + 1)


def has_triangle_recursive(A):
    if len(A) < 3:
        return 0
    return loop_P(A, 0)


print(has_triangle_recursive([10, 50, 1]))           
print(has_triangle_recursive([10, 2, 5, 1, 8, 20]))  
```

### Analysis & Complexity (Steps)

Let's count the work.

| Step | What it does | Cost |
|------|--------------|------|
| 1 | Each recursive call does a constant amount of work (a few `if` checks) | `O(1)` per call |
| 2 | `loop_P` runs about `N` times | `N` |
| 3 | For each `P`, `loop_Q` runs about `N` times | `N × N` |
| 4 | For each `(P, Q)`, `loop_R` runs about `N` times | `N × N × N` |
| 5 | Inside `loop_R`, `is_triangle` is `O(1)` | constant |

So the total number of recursive calls is about `N × N × N = N³`.

More precisely, the number of valid triplets `(P, Q, R)` with `P < Q < R` is `C(N, 3) = N(N−1)(N−2)/6`, which is also `O(N³)`.

**Total time complexity:**

```
O(N³)
```

---

## 4. Comparison Between the Two Algorithms

| Aspect | Algorithm 1 (Bubble Sort + Scan) | Algorithm 2 (Recursive Brute Force) |
|--------|----------------------------------|-------------------------------------|
| Approach | Fully iterative (loops only) | Fully recursive (function calls itself) |
| Main idea | Sort first, then check only consecutive triplets | Try every possible triplet |
| Time complexity | `O(N²)` | `O(N³)` |
| Simplicity of idea | A little tricky (needs the math insight) | Very direct and obvious |
| Best use case | Most arrays (general use) | Small arrays / teaching recursion |

### Final Verdict

The **bubble sort + scan algorithm is much faster** because `O(N²)` grows much slower than `O(N³)`.

The **recursive brute force algorithm** is easier to understand — it just tries everything. But it becomes unusable when the array is big.

In practice, we always prefer **Algorithm 1 (Bubble Sort + Scan)** for this problem.

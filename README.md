# Triangle Triplet Detection

## Problem Statement

Given a zero-indexed array A of N integers, determine if there exists a triangular triplet (P, Q, R) where:
- A[P] + A[Q] > A[R]
- A[Q] + A[R] > A[P]
- A[R] + A[P] > A[Q]

Return 1 if such triplet exists, 0 otherwise.

---

## Algorithm 1: Iterative Brute Force

### Implementation

```python
def solution_iterative(nums):
    n = len(nums)
    
    for p in range(n):
        for q in range(p + 1, n):
            for r in range(q + 1, n):
                if (nums[p] + nums[q] > nums[r] and
                    nums[q] + nums[r] > nums[p] and
                    nums[r] + nums[p] > nums[q]):
                    return 1
    
    return 0
```

### Pseudo-code

```
FUNCTION solution_iterative(nums):
    n = length(nums)
    
    FOR p FROM 0 TO n-1:
        FOR q FROM p+1 TO n-1:
            FOR r FROM q+1 TO n-1:
                IF nums[p] + nums[q] > nums[r] AND
                   nums[q] + nums[r] > nums[p] AND
                   nums[r] + nums[p] > nums[q]:
                    RETURN 1
    
    RETURN 0
```

### Time Complexity Analysis

#### Best Case: O(1)

**Step 1**: First iteration finds valid triplet at indices (0, 1, 2)
```
p = 0, q = 1, r = 2
Check conditions: 3 comparisons + 2 AND operations = 5 operations
```

**Step 2**: Return immediately
```
Total operations = 5 = O(1)
```

#### Average Case: O(n³)

**Step 1**: Assume valid triplet exists at middle position
```
Expected position: approximately n/2 combinations checked
```

**Step 2**: Count loop iterations up to average position
```
Outer loop: runs approximately n/2 times
Middle loop: for each p, runs approximately (n-p)/2 times
Inner loop: for each (p,q), runs approximately (n-q)/2 times
```

**Step 3**: Calculate total iterations
```
Average iterations ≈ (n/2) × (n/2) × (n/2) = n³/8
Coefficient 1/8 is dropped in Big-O notation
Average case = O(n³)
```

#### Worst Case: O(n³)

**Step 1**: No valid triplet exists, must check all combinations
```
Outer loop (p): runs from 0 to n-1 = n iterations
```

**Step 2**: For each p, middle loop runs
```
When p=0: q runs from 1 to n-1 = (n-1) iterations
When p=1: q runs from 2 to n-1 = (n-2) iterations
...
When p=n-2: q runs from n-1 to n-1 = 1 iteration
Total middle iterations = (n-1) + (n-2) + ... + 1 = n(n-1)/2
```

**Step 3**: For each (p,q) pair, inner loop runs
```
When p=0, q=1: r runs from 2 to n-1 = (n-2) iterations
When p=0, q=2: r runs from 3 to n-1 = (n-3) iterations
...
Pattern continues for all valid (p,q) pairs
```

**Step 4**: Calculate total combinations
```
Total = C(n,3) = n!/(3!(n-3)!)
     = n(n-1)(n-2)/6
     = (n³ - 3n² + 2n)/6
     = n³/6 - n²/2 + n/3
```

**Step 5**: Apply Big-O notation
```
Drop lower order terms: n³/6 - n²/2 + n/3 ≈ n³/6
Drop constant coefficient: n³/6 → n³
Worst case = O(n³)
```

### Space Complexity Analysis

#### Step 1: Identify variables
```
n: integer (4 or 8 bytes)
p: integer (4 or 8 bytes)
q: integer (4 or 8 bytes)
r: integer (4 or 8 bytes)
nums: reference/pointer (not counted, passed as parameter)
```

#### Step 2: Calculate total space
```
Total space = 4 variables × 8 bytes = 32 bytes
```

#### Step 3: Determine growth with input size
```
Space used = 32 bytes (constant, independent of n)
Space complexity = O(1)
```

---

## Algorithm 2: Recursive Backtracking

### Implementation

```python
def solution_recursive(nums):
    def find_triplet(index, selected):
        if len(selected) == 3:
            a, b, c = selected[0], selected[1], selected[2]
            if (nums[a] + nums[b] > nums[c] and
                nums[b] + nums[c] > nums[a] and
                nums[c] + nums[a] > nums[b]):
                return True
            return False
        
        if index >= len(nums):
            return False
        
        if find_triplet(index + 1, selected + [index]):
            return True
        
        if find_triplet(index + 1, selected):
            return True
        
        return False
    
    return 1 if find_triplet(0, []) else 0
```

### Pseudo-code

```
FUNCTION solution_recursive(nums):
    FUNCTION find_triplet(index, selected):
        IF length(selected) == 3:
            a, b, c = selected[0], selected[1], selected[2]
            IF nums[a] + nums[b] > nums[c] AND
               nums[b] + nums[c] > nums[a] AND
               nums[c] + nums[a] > nums[b]:
                RETURN True
            RETURN False
        
        IF index >= length(nums):
            RETURN False
        
        IF find_triplet(index + 1, selected + [index]):
            RETURN True
        
        IF find_triplet(index + 1, selected):
            RETURN True
        
        RETURN False
    
    RETURN 1 IF find_triplet(0, []) ELSE 0
```

### Time Complexity Analysis

#### Best Case: O(1)

**Step 1**: First three elements form valid triplet
```
Recursion path: include index 0, include index 1, include index 2
Recursion depth = 3
```

**Step 2**: At depth 3, selected = [0, 1, 2]
```
Check triangle conditions: 3 comparisons + 2 AND operations = 5 operations
Return True immediately
```

**Step 3**: Unwind recursion
```
Total operations = 3 (recursive calls) + 5 (condition check) = 8 = O(1)
```

#### Average Case: O(n³) practical

**Step 1**: Recursion tree structure
```
Each node has 2 branches: include current index OR skip current index
Maximum depth = n (one path per element)
```

**Step 2**: Pruning occurs when selected.length = 3
```
Without pruning: 2ⁿ nodes in complete binary tree
With pruning: only explore until 3 elements selected
```

**Step 3**: Count nodes explored (with pruning)
```
Nodes at depth 0: C(n,0) = 1
Nodes at depth 1: C(n,1) = n
Nodes at depth 2: C(n,2) = n(n-1)/2
Nodes at depth 3: C(n,3) = n(n-1)(n-2)/6
Total nodes ≈ n³/6
```

**Step 4**: Each node performs constant work
```
Average case = O(n³/6) = O(n³)
```

#### Worst Case: O(2ⁿ) theoretical, O(n³) practical

**Step 1**: Without early termination (theoretical)
```
Each element: 2 choices (include or exclude)
Total paths = 2ⁿ
Each path: O(1) work
Theoretical worst = O(2ⁿ)
```

**Step 2**: With early termination at depth 3 (practical)
```
Only explore combinations where selected ≤ 3
Maximum nodes = C(n,0) + C(n,1) + C(n,2) + C(n,3)
                = 1 + n + n(n-1)/2 + n(n-1)(n-2)/6
                ≈ n³/6 (dominant term)
Practical worst = O(n³)
```

**Step 3**: Comparison operations
```
For each triplet found: 3 comparisons
Total triplets checked = C(n,3) = n(n-1)(n-2)/6
Total operations ≈ 3 × n³/6 = n³/2
Worst case = O(n³)
```

### Space Complexity Analysis

#### Step 1: Recursion stack depth
```
Maximum recursion depth = n (visit each element once)
Each stack frame stores:
  - index: integer (8 bytes)
  - selected: list of at most 3 integers (3 × 8 = 24 bytes)
  - return address and local variables: ~32 bytes
Total per frame ≈ 64 bytes
```

#### Step 2: Maximum stack size
```
Stack space = depth × bytes_per_frame
            = n × 64 bytes
            = 64n bytes
```

#### Step 3**: Selected list storage
```
selected list: grows to maximum 3 elements
List overhead: ~8 bytes per element
Maximum selected storage = 3 × 8 = 24 bytes (constant)
```

#### Step 4: Total space calculation
```
Total space = recursion stack + selected list
            = O(n) + O(1)
            = O(n)
```

**Space complexity = O(n)**

---

## Algorithm 3: Optimized Sorting Approach

### Implementation

```python
def solution_optimized(nums):
    if len(nums) < 3:
        return 0
    
    nums_sorted = sorted(nums)
    
    for i in range(len(nums_sorted) - 2):
        if nums_sorted[i] + nums_sorted[i + 1] > nums_sorted[i + 2]:
            return 1
    
    return 0
```

### Pseudo-code

```
FUNCTION solution_optimized(nums):
    IF length(nums) < 3:
        RETURN 0
    
    nums_sorted = SORT(nums)
    
    FOR i FROM 0 TO length(nums_sorted) - 3:
        IF nums_sorted[i] + nums_sorted[i+1] > nums_sorted[i+2]:
            RETURN 1
    
    RETURN 0
```

### Time Complexity Analysis

#### Best Case: O(n log n)

**Step 1**: Array already sorted or first triplet is valid
```
Sorting phase: O(n log n) - cannot be avoided even if sorted
```

**Step 2**: First iteration finds valid triplet
```
i = 0: check nums_sorted[0] + nums_sorted[1] > nums_sorted[2]
Operations: 1 addition + 1 comparison = 2 operations = O(1)
```

**Step 3**: Return immediately
```
Total = sorting + first check
      = O(n log n) + O(1)
      = O(n log n)
```

**Best case = O(n log n)**

#### Average Case: O(n log n)

**Step 1**: Sorting phase
```
Algorithm: typically merge sort or tim sort
Divide step: log₂(n) levels
Merge step: O(n) work per level
Total sorting = n × log₂(n) = O(n log n)
```

**Step 2**: Loop through approximately half the array
```
Loop iterations: (n-2)/2 on average
Each iteration: 1 addition + 1 comparison = 2 operations
Loop cost = (n-2)/2 × 2 = n - 2 = O(n)
```

**Step 3**: Combine phases
```
Total = sorting + loop
      = O(n log n) + O(n)
      = O(n log n) (dominant term)
```

**Average case = O(n log n)**

#### Worst Case: O(n log n)

**Step 1**: Sorting phase (worst case)
```
Merge sort worst case: always O(n log n)
  - Divide: log₂(n) recursive levels
  - Merge: n comparisons per level
  - Total: n × log₂(n)
```

**Step 2**: Full loop traversal (no valid triplet)
```
Loop runs from i = 0 to i = n-3
Total iterations = n - 2
Each iteration:
  - Array access: nums_sorted[i], nums_sorted[i+1], nums_sorted[i+2] = 3 operations
  - Addition: nums_sorted[i] + nums_sorted[i+1] = 1 operation
  - Comparison: result > nums_sorted[i+2] = 1 operation
  - Total per iteration = 5 operations
Total loop cost = (n-2) × 5 = 5n - 10 = O(n)
```

**Step 3**: Combine phases
```
Total = sorting + complete loop
      = O(n log n) + O(n)
      = O(n log n) (dominant term)
```

**Worst case = O(n log n)**

### Space Complexity Analysis

#### Step 1: Sorted array storage
```
Original array: n elements (not modified)
Sorted copy: nums_sorted = n elements
Each element: 4 or 8 bytes (integer)
Sorted array space = n × 8 bytes = 8n bytes
```

#### Step 2: Sorting algorithm auxiliary space
```
Merge sort: requires O(n) auxiliary space for merging
Temporary arrays during merge: n elements
Total sorting auxiliary = n × 8 bytes = 8n bytes
```

**Step 3**: Loop variables
```
i: integer = 8 bytes (constant)
```

#### Step 4: Total space calculation
```
Total space = sorted_array + merge_auxiliary + loop_vars
            = O(n) + O(n) + O(1)
            = O(n) + O(n)
            = O(n)
```

**Space complexity = O(n)**

---

## Comparison

### Complexity Summary Table

| Algorithm | Best Case | Average Case | Worst Case | Space |
|-----------|-----------|--------------|------------|-------|
| Iterative Brute Force | O(1) | O(n³) | O(n³) | O(1) |
| Recursive Backtracking | O(1) | O(n³) practical | O(n³) practical, O(2ⁿ) theoretical | O(n) |
| Optimized Sorting | O(n log n) | O(n log n) | O(n log n) | O(n) |

### Detailed Complexity Breakdown

#### Time Complexity Calculations

**Iterative Brute Force:**
- Best: 5 operations (constant) → O(1)
- Average: n³/8 iterations → O(n³)
- Worst: n(n-1)(n-2)/6 ≈ n³/6 iterations → O(n³)

**Recursive Backtracking:**
- Best: 8 operations (3 calls + 5 checks) → O(1)
- Average: C(n,3) = n(n-1)(n-2)/6 triplet checks → O(n³)
- Worst: All combinations explored → O(n³) practical

**Optimized Sorting:**
- Best: n log n (sorting) + 2 (first check) → O(n log n)
- Average: n log n (sorting) + n (half loop) → O(n log n)
- Worst: n log n (sorting) + 5(n-2) (full loop) → O(n log n)

#### Space Complexity Calculations

**Iterative Brute Force:**
```
4 variables × 8 bytes = 32 bytes = O(1)
```

**Recursive Backtracking:**
```
Stack depth: n
Stack frame size: 64 bytes
Total: 64n bytes = O(n)
```

**Optimized Sorting:**
```
Sorted array: 8n bytes
Merge auxiliary: 8n bytes
Total: 16n bytes = O(n)
```

### Performance Analysis

**Iterative Brute Force**:
- Pros: Simple, no extra space, deterministic
- Cons: Cubic time complexity, inefficient for large arrays
- When to use: Small arrays (n < 50), memory-constrained environments

**Recursive Backtracking**:
- Pros: Elegant, demonstrates recursion, early termination
- Cons: Stack space overhead, potential stack overflow (n > 1000)
- When to use: Educational purposes, small to medium arrays

**Optimized Sorting**:
- Pros: Best time complexity, scalable, predictable performance
- Cons: Requires extra space, sorting overhead
- When to use: Large arrays (n > 100), production environments

### Mathematical Insight (Optimized Algorithm)

**Why sorting enables O(n) checking:**

In a sorted array [a, b, c] where a ≤ b ≤ c:
```
If a + b > c (smallest + middle > largest)
Then:
  b + c > a  (automatically true, since b ≥ a and c > 0)
  a + c > b  (automatically true, since c ≥ b and a > 0)
```

Therefore, only ONE condition needs to be checked per sorted triplet.

### Empirical Performance (n = 1000)

| Algorithm | Operations | Relative Speed |
|-----------|-----------|----------------|
| Iterative | ~166,666,666 | 1x (baseline) |
| Recursive | ~166,666,666 | 1x (similar) |
| Optimized | ~10,000 | 16,666x faster |

### Recommendation

For **small arrays (n < 100)**: Iterative approach is acceptable  
For **large arrays (n > 100)**: Optimized sorting approach is mandatory  
For **educational purposes**: Recursive demonstrates backtracking pattern

---

## Test Cases

```python
print(solution_iterative([10, 50, 1]))
print(solution_iterative([10, 2, 5, 1, 8, 20]))

print(solution_recursive([10, 50, 1]))
print(solution_recursive([10, 2, 5, 1, 8, 20]))

print(solution_optimized([10, 50, 1]))
print(solution_optimized([10, 2, 5, 1, 8, 20]))
```

Expected Output:
```
0
1
0
1
0
1
```

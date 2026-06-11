# Contest Analysis: April 2026

This archive contains the refined analysis of Weekly Contest 499.

---

## 🗓️ Weekly Contest 499

### Q1. Width of Columns in Grid
**Difficulty:** 🟢 Easy | **Pattern:** Array / Matrix

#### Actual Problem Statement
You are given a 0-indexed `m x n` integer matrix `grid`. The width of a column is the maximum length of the string representation of its integers. For example, the width of the column `[1, 10, 100]` is 3.

Return an integer array `ans` of size `n` where `ans[j]` is the width of the `j`-th column.

**Constraints:**
- $m == grid.length$
- $n == grid[i].length$
- $1 \leq m, n \leq 100$
- $-10^9 \leq grid[i][j] \leq 10^9$

---

#### Multi-level Solutions

**Optimal (Simulation)**
Iterate through each column, convert every element to its string representation, and track the maximum length encountered.
- **Logic:** For each column $j$, traverse all rows $i$. Calculate `len(str(grid[i][j]))` and update `max_width`.
- **Complexity:** $O(M \cdot N \cdot L)$, where $L$ is the maximum number of digits (approx. 11 for $10^9$).
- **Code:**
```python
def findColumnWidth(grid):
    m, n = len(grid), len(grid[0])
    res = [0] * n
    for j in range(n):
        max_w = 0
        for i in range(m):
            # String conversion handles negative signs correctly
            max_w = max(max_w, len(str(grid[i][j])))
        res[j] = max_w
    return res
```

#### Interview Traps
1. **Negative Signs**: In LeetCode, the negative sign `-` counts as a character. `str(-123)` has length 4.
2. **Leading Zeros**: Ensure you are using the actual integer values; string conversion of the number itself is the safest way to get the display width.

---

### Q2. Score All Prefixes of an Array
**Difficulty:** 🟡 Medium | **Pattern:** Prefix Sum / Greedy

#### Actual Problem Statement
We define the conversion array `conver` of an array `arr` as follows: `conver[i] = arr[i] + max(arr[0..i])`.
The **score** of a prefix `arr[0..i]` is the sum of `conver[0..i]`.

Return an array `ans` where `ans[i]` is the score of the `i`-th prefix of `nums`.

**Constraints:**
- $1 \leq nums.length \leq 10^5$
- $1 \leq nums[i] \leq 10^9$

---

#### Multi-level Solutions

**Brute Force**
First, construct the `conver` array by iterating through `nums` and keeping track of the running maximum. Then, compute the prefix sums of `conver`.
- **Complexity:** $O(N)$ time and $O(N)$ extra space for the `conver` array.
- **Code:**
```python
def findPrefixScore(nums):
    n = len(nums)
    conver = [0] * n
    curr_max = 0
    for i in range(n):
        curr_max = max(curr_max, nums[i])
        conver[i] = nums[i] + curr_max
    
    ans = [0] * n
    curr_sum = 0
    for i in range(n):
        curr_sum += conver[i]
        ans[i] = curr_sum
    return ans
```

**Optimal (One-Pass)**
We can combine both steps into a single loop to reduce space overhead (though $O(N)$ for the result is mandatory).
- **Logic:** Maintain `running_max` and `running_score_sum` while iterating.
- **Complexity:** $O(N)$ time and $O(1)$ extra space (excluding result).
- **Code:**
```python
def findPrefixScoreOptimal(nums):
    n = len(nums)
    ans = [0] * n
    running_max = 0
    running_score_sum = 0
    for i in range(n):
        running_max = max(running_max, nums[i])
        conver_val = nums[i] + running_max
        running_score_sum += conver_val
        ans[i] = running_score_sum
    return ans
```

#### Interview Traps
1. **Integer Overflow**: Since $nums[i] \leq 10^9$ and $n \leq 10^5$, the prefix sum can reach $2 \cdot 10^{14}$, which exceeds the range of a 32-bit integer. Always use 64-bit integers (`long long` in C++, `long` in Java).
2. **Definition Misunderstanding**: Some might think `conver[i] = max(arr[0..i])`. Read carefully: it is `arr[i]` PLUS the maximum.

---

### Q3. Minimize Maximum of Array II
**Difficulty:** 🟡 Medium | **Pattern:** Binary Search on Answer / Greedy

#### Actual Problem Statement
You are given a 0-indexed array `nums` comprising `n` non-negative integers. In one operation, you must:
1. Choose an integer `i` such that $1 \leq i < n$ and $nums[i] > 0$.
2. Decrease `nums[i]` by 1.
3. Increase `nums[i - 1]` by 1.

Return the minimum possible value of the maximum integer of `nums` after performing any number of operations.

**Constraints:**
- $n == nums.length, 2 \leq n \leq 10^5$
- $0 \leq nums[i] \leq 10^9$

---

#### Multi-level Solutions

**Optimal (Binary Search on Answer)**
Binary search for the minimum possible maximum value $X$.
- **Logic:** To check if $X$ is possible, iterate from right to left. If $nums[i] > X$, we must move the excess $nums[i] - X$ to $nums[i-1]$. If at the end, $nums[0] \leq X$, then $X$ is feasible.
- **Complexity:** $O(N \log(\max(nums)))$.
- **Code:**
```python
def minimizeArrayValue(nums):
    def check(limit):
        extra = 0
        for i in range(len(nums) - 1, 0, -1):
            extra = max(0, nums[i] + extra - limit)
        return nums[0] + extra <= limit

    low, high = 0, max(nums)
    ans = high
    while low <= high:
        mid = (low + high) // 2
        if check(mid):
            ans = mid
            high = mid - 1
        else:
            low = mid + 1
    return ans
```

**Optimal (Prefix Sums / Mathematical)**
Notice that the total sum of any prefix $nums[0 \dots i]$ can be distributed such that the values are nearly equal. The maximum value in that prefix will be at least $\lceil \text{sum}(nums[0 \dots i]) / (i+1) \rceil$.
- **Complexity:** $O(N)$.
- **Code:**
```python
def minimizeArrayValueOptimal(nums):
    res = 0
    total_sum = 0
    for i in range(len(nums)):
        total_sum += nums[i]
        # Integer ceiling division: (a + b - 1) // b
        res = max(res, (total_sum + i) // (i + 1))
    return res
```

#### Interview Traps
1. **One-way Transfer**: You can only move values from $i$ to $i-1$. This means you can "push" excess values to the left, but never to the right.
2. **Floating Point Precision**: When using the prefix sum approach, use `(total_sum + i) // (i + 1)` for integer ceiling division to avoid precision issues with large numbers.

---

### Q4. Number of Ways to Earn Points
**Difficulty:** 🔴 Hard | **Pattern:** Dynamic Programming / Bounded Knapsack

#### Actual Problem Statement
There is an exam that has `n` types of questions. You are given an integer `target` and a 2D integer array `types` where `types[i] = [count_i, marks_i]` indicates that there are `count_i` questions of the `i`-th type, and each question awards `marks_i` marks.

Return the number of ways you can earn exactly `target` marks. Return the answer modulo $10^9 + 7$.

**Constraints:**
- $1 \leq target \leq 1000$
- $1 \leq n == types.length \leq 50$
- $1 \leq count_i, marks_i \leq 50$

---

#### Multi-level Solutions

**Optimal (Dynamic Programming)**
This is a classic Bounded Knapsack problem where we want to find the number of ways to reach a total.
- **State:** `dp[i][j]` is the number of ways to get `j` marks using the first `i` types of questions.
- **Transition:** $dp[i][j] = \sum_{k=0}^{count_i} dp[i-1][j - k \cdot marks_i]$.
- **Space Optimization:** Use a 1D DP array and iterate backwards to prevent using the same type's results multiple times in a single step.
- **Complexity:** $O(N \cdot \text{Target} \cdot \text{Count})$.
- **Code:**
```python
def waysToReachTarget(target, types):
    MOD = 10**9 + 7
    dp = [0] * (target + 1)
    dp[0] = 1
    
    for count, marks in types:
        for j in range(target, 0, -1):
            for k in range(1, count + 1):
                if j >= k * marks:
                    dp[j] = (dp[j] + dp[j - k * marks]) % MOD
                else:
                    break
    return dp[target]
```

#### Interview Traps
1. **Bounded vs. Unbounded**: This is Bounded Knapsack (limited `count_i`). If `count_i` were infinite, it would be Unbounded Knapsack, solvable in $O(N \cdot \text{Target})$.
2. **Modulo Application**: Ensure the modulo is applied at each addition to prevent overflow and maintain correctness.
3. **Space Complexity**: Forgetting to iterate backwards in the 1D DP array (a common mistake in knapsack-style DP).

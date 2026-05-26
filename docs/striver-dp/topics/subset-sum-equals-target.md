# Subset Sum Equals Target (DP on Subsequences)

## 🧩 Problem Summary
- **Problem:** Given an array of $N$ positive integers and a target $K$, check if there exists a subset whose sum equals $K$.
- **Subset vs Subsequence:** A subset is a selection of elements from the array (order doesn't matter for sum), whereas a subsequence follows the array's order. For this problem, both terms are used interchangeably.
- **Input:** An array `arr` and an integer `K`.
- **Output:** Boolean (`True` if a subset exists, `False` otherwise).

## 💡 Intuition
- **The Core Idea:** For every element in the array, we have a simple binary choice:
  1. **Pick (Take):** Include the element in our subset and reduce the target.
  2. **Not Pick (Not Take):** Skip the element and keep the target the same.
- **Why Greedy Fails:** There is no uniform way to pick elements. Picking the largest available element might prevent you from forming the target with smaller elements, and vice versa.
- **Thought Process:**
  - We represent the state as `f(index, target)`, meaning: "Can we form `target` using elements from `0` to `index`?"
  - If we "take" `arr[index]`, the new state is `f(index - 1, target - arr[index])`.
  - If we "not take", the new state is `f(index - 1, target)`.

## 🔁 Approach

### 1. Recursion
- **Explanation:** Try both "take" and "not take" for every index. If either returns `True`, then the result for that state is `True`.
- **Code (Python):**
```python
def solve(index, target, arr):
    if target == 0:
        return True
    if index == 0:
        return arr[0] == target
    
    not_take = solve(index - 1, target, arr)
    take = False
    if arr[index] <= target:
        take = solve(index - 1, target - arr[index], arr)
        
    return take or not_take
```

### 2. Memoization (Top-Down DP)
- **Explanation:** Use a 2D `dp` table initialized with -1 to store the results of `(index, target)`.
- **Code (Python):**
```python
def solve(index, target, arr, dp):
    if target == 0:
        return True
    if index == 0:
        return arr[0] == target
    
    if dp[index][target] != -1:
        return dp[index][target]
    
    not_take = solve(index - 1, target, arr, dp)
    take = False
    if arr[index] <= target:
        take = solve(index - 1, target - arr[index], arr, dp)
        
    dp[index][target] = take or not_take
    return dp[index][target]
```

### 3. Tabulation (Bottom-Up DP)
- **Explanation:** Create a boolean table and fill it based on the base cases.
- **Code (Python):**
```python
def subsetSumToK(n, k, arr):
    dp = [[False for _ in range(k + 1)] for _ in range(n)]
    
    # Base case 1: Target is 0
    for i in range(n):
        dp[i][0] = True
        
    # Base case 2: Index 0
    if arr[0] <= k:
        dp[0][arr[0]] = True
        
    for i in range(1, n):
        for target in range(1, k + 1):
            not_take = dp[i-1][target]
            take = False
            if arr[i] <= target:
                take = dp[i-1][target - arr[i]]
            dp[i][target] = take or not_take
            
    return dp[n-1][k]
```

### 4. Space Optimization
- **Explanation:** Each row in the `dp` table only depends on the row above it. We can use a single array `prev` to keep track of the results for `index - 1`.
- **Code (Python):**
```python
def subsetSumToK(n, k, arr):
    prev = [False] * (k + 1)
    prev[0] = True
    
    if arr[0] <= k:
        prev[arr[0]] = True
        
    for i in range(1, n):
        cur = [False] * (k + 1)
        cur[0] = True # Target 0 is always possible
        for target in range(1, k + 1):
            not_take = prev[target]
            take = False
            if arr[i] <= target:
                take = prev[target - arr[i]]
            cur[target] = take or not_take
        prev = cur
        
    return prev[k]
```

## 🧠 DP State Definition
- `dp[i][target]` is a boolean representing whether a subset with sum `target` can be formed using elements from the first `i` indices (0 to `i`) of the array.

## 🔄 Recurrence Relation
- `f(i, target) = f(i-1, target) || f(i-1, target - arr[i])` (if $arr[i] \le target$)
- **Base Cases:**
  - `if target == 0`: `True`
  - `if i == 0`: `True` if `arr[0] == target`, else `False`

## ⏱️ Complexity
- **Time Complexity:** $O(N \times K)$ where $N$ is the number of elements and $K$ is the target sum.
- **Space Complexity:**
  - $O(N \times K)$ for Tabulation/Memoization.
  - $O(K)$ for Space Optimization.

## 📌 Pattern
- **DP on Subsequences:** The Pick/Not-Pick pattern.

## ⚠️ Common Mistakes
- **Target 0 in Tabulation:** Always ensure `dp[i][0] = True` because an empty subset can always form a sum of 0.
- **Boundary Check:** Forgetting to check `arr[i] <= target` before accessing `dp[i-1][target - arr[i]]`.
- **Boolean vs Int:** In some languages, `dp` should be an integer table for memoization to handle the "unvisited" state (-1).

## 🔗 Related Problems
- [Partition Equal Subset Sum](https://leetcode.com/problems/partition-equal-subset-sum/)
- [Target Sum](https://leetcode.com/problems/target-sum/)
- [Coin Change 2](https://leetcode.com/problems/coin-change-2/)

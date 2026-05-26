# Partition Equal Subset Sum (DP on Subsequences)

## 🧩 Problem Summary
- **Problem:** Given an array of $N$ positive integers, determine if it can be partitioned into two subsets such that the sum of elements in both subsets is equal.
- **Input:** An array `arr`.
- **Output:** Boolean (`True` if partition is possible, `False` otherwise).
- **Example:** `arr = [2, 3, 3, 3, 4, 5]`. Total sum = 20. Subsets `[2, 3, 5]` and `[3, 3, 4]` both sum to 10. Output: `True`.

## 💡 Intuition
- **Key Insight:** If an array can be partitioned into two equal subsets, the sum of each subset must be exactly half of the total sum of the array ($S/2$).
- **Mathematical Condition:**
  - If the total sum $S$ is **odd**, it is impossible to partition it into two equal integer sums. Return `False` immediately.
  - If the total sum $S$ is **even**, the problem reduces to finding if there exists a subset with a sum exactly equal to $S/2$.
- **Reduction:** This problem is a direct application of the **Subset Sum Equals Target** problem, where the target is $S/2$.

## 🔁 Approach

### 1. Recursion
- **Explanation:** Calculate total sum, check if odd, then use recursion to find a subset with sum `totalSum // 2`.
- **Code (Python):**
```python
def solve(index, target, arr):
    if target == 0: return True
    if index == 0: return arr[0] == target
    
    not_take = solve(index - 1, target, arr)
    take = False
    if arr[index] <= target:
        take = solve(index - 1, target - arr[index], arr)
    return take or not_take

def canPartition(arr, n):
    total_sum = sum(arr)
    if total_sum % 2 != 0: return False
    return solve(n - 1, total_sum // 2, arr)
```

### 2. Memoization (Top-Down DP)
- **Explanation:** Use a 2D `dp` table to store results of `(index, target)` to avoid redundant calculations.
- **Code (Python):**
```python
def solve(index, target, arr, dp):
    if target == 0: return True
    if index == 0: return arr[0] == target
    if dp[index][target] != -1: return dp[index][target]
    
    not_take = solve(index - 1, target, arr, dp)
    take = False
    if arr[index] <= target:
        take = solve(index - 1, target - arr[index], arr, dp)
    
    dp[index][target] = take or not_take
    return dp[index][target]
```

### 3. Tabulation (Bottom-Up DP)
- **Explanation:** Build a boolean table where `dp[i][j]` is true if a subset of sum `j` can be formed using elements up to index `i`.
- **Code (Python):**
```python
def canPartition(arr, n):
    total_sum = sum(arr)
    if total_sum % 2 != 0: return False
    k = total_sum // 2
    
    dp = [[False for _ in range(k + 1)] for _ in range(n)]
    for i in range(n): dp[i][0] = True
    if arr[0] <= k: dp[0][arr[0]] = True
    
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
- **Explanation:** Since `dp[i]` only depends on `dp[i-1]`, we can use two rows (`prev` and `cur`).
- **Code (Python):**
```python
def canPartition(arr, n):
    total_sum = sum(arr)
    if total_sum % 2 != 0: return False
    k = total_sum // 2
    
    prev = [False] * (k + 1)
    prev[0] = True
    if arr[0] <= k: prev[arr[0]] = True
    
    for i in range(1, n):
        cur = [False] * (k + 1)
        cur[0] = True
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
- `dp[i][target]` represents whether a subset with sum `target` can be formed using elements from the first `i` indices of the array.

## 🔄 Recurrence Relation
- $f(i, target) = f(i-1, target) \lor f(i-1, target - arr[i])$ (if $arr[i] \le target$)
- **Base Case:** 
  - `target == 0` is always `True`.
  - `index == 0` is `True` if `arr[0] == target`.

## ⏱️ Complexity
- **Time Complexity:** $O(N \times \frac{\text{TotalSum}}{2})$ for the DP table filling, plus $O(N)$ for the total sum calculation.
- **Space Complexity:**
  - $O(N \times \frac{\text{TotalSum}}{2})$ for Tabulation/Memoization.
  - $O(\frac{\text{TotalSum}}{2})$ for Space Optimization.

## 📌 Pattern
- **DP on Subsequences:** Subset Sum variation.

## ⚠️ Common Mistakes
- **Odd Sum Check:** Forgetting to return `False` if the total sum is odd.
- **Target Value:** Using `totalSum` as the target instead of `totalSum / 2`.
- **Large Array Elements:** In space optimization, ensure `arr[0]` is checked against `k` before marking `prev[arr[0]] = True` to avoid index out of bounds.

## 🔗 Related Problems
- [Subset Sum Equals Target](https://leetcode.com/problems/subset-sum-equals-target/)
- [Partition Array Into Two Subsets With Minimum Absolute Sum Difference](https://leetcode.com/problems/partition-array-into-two-subsets-to-minimize-sum-difference/)
- [Target Sum](https://leetcode.com/problems/target-sum/)

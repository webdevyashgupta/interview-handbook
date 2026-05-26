# Partition Set Into Two Subsets With Minimum Absolute Sum Difference (DP on Subsequences)

## 🧩 Problem Summary
- **Problem:** Given an array of $N$ non-negative integers, partition the array into two subsets $S_1$ and $S_2$ such that the absolute difference between their sums $|sum(S_1) - sum(S_2)|$ is minimized.
- **Input:** An array `arr`.
- **Output:** The minimum absolute difference.

## 💡 Intuition
- **The Core Idea:** Let the total sum of the array be $T$. If the sum of subset $S_1$ is $s_1$, then the sum of $S_2$ must be $T - s_1$.
- **The Objective:** We want to minimize $|s_1 - (T - s_1)|$, which simplifies to $|2s_1 - T|$.
- **Constraints:** To minimize the difference, $s_1$ should be as close to $T/2$ as possible.
- **Connection to Subset Sum:** This problem can be solved if we know all possible subset sums that can be formed using the array elements. If we can form a sum $s_1$, we can calculate the potential difference.

## 🔁 Approach

### 1. Tabulation (Bottom-Up DP)
- **Explanation:** We use the same tabulation approach as the "Subset Sum Equals Target" problem. We fill a `dp` table of size `n x (T + 1)` where `dp[i][j]` is `True` if a sum `j` can be formed using elements up to index `i`.
- **Final Row Analysis:** After filling the table, the last row `dp[n-1]` tells us all achievable sums from `0` to `T`.
- **Code (Python):**
```python
def minSubsetSumDifference(arr, n):
    total_sum = sum(arr)
    k = total_sum
    
    # Standard Subset Sum Tabulation
    dp = [[False for _ in range(k + 1)] for _ in range(n)]
    for i in range(n):
        dp[i][0] = True
    if arr[0] <= k:
        dp[0][arr[0]] = True
        
    for i in range(1, n):
        for target in range(1, k + 1):
            not_take = dp[i-1][target]
            take = False
            if arr[i] <= target:
                take = dp[i-1][target - arr[i]]
            dp[i][target] = take or not_take
            
    # dp[n-1][col] contains True if sum 'col' is possible
    
    mini = int(1e9)
    # Check all possible sums in the last row
    for s1 in range(total_sum + 1):
        if dp[n-1][s1]:
            diff = abs(s1 - (total_sum - s1))
            mini = min(mini, diff)
            
    return mini
```

### 2. Space Optimization
- **Explanation:** Since each row only depends on the previous row, we can optimize space to use only one array.
- **Code (Python):**
```python
def minSubsetSumDifference(arr, n):
    total_sum = sum(arr)
    prev = [False] * (total_sum + 1)
    prev[0] = True
    
    if arr[0] <= total_sum:
        prev[arr[0]] = True
        
    for i in range(1, n):
        cur = [False] * (total_sum + 1)
        cur[0] = True
        for target in range(1, total_sum + 1):
            not_take = prev[target]
            take = False
            if arr[i] <= target:
                take = prev[target - arr[i]]
            cur[target] = take or not_take
        prev = cur
        
    mini = int(1e9)
    for s1 in range(total_sum // 2 + 1):
        if prev[s1]:
            s2 = total_sum - s1
            mini = min(mini, abs(s1 - s2))
            
    return mini
```

## 🧠 DP State Definition
- `dp[i][j]` is a boolean representing whether a subset with sum `j` can be formed using elements from the first `i` indices.

## 🔄 Recurrence Relation
- `f(i, target) = f(i-1, target) || f(i-1, target - arr[i])`
- **Base Cases:**
  - `target == 0`: `True`
  - `i == 0`: `True` if `arr[0] == target`, else `False`

## ⏱️ Complexity
- **Time Complexity:** $O(N \times \text{TotalSum})$ to fill the `dp` table, plus $O(\text{TotalSum})$ to find the minimum difference.
- **Space Complexity:** $O(N \times \text{TotalSum})$ for tabulation, or $O(\text{TotalSum})$ with space optimization.

## 📌 Pattern
- **DP on Subsequences:** Finding all possible sums using a pick/not-pick strategy.

## ⚠️ Common Mistakes
- **Range of search:** We only need to check sums up to `total_sum / 2` to find the minimum difference.
- **Dependency:** Ensure you have mastered the "Subset Sum" problem before attempting this, as it is a direct application.

## 🔗 Related Problems
- [Subset Sum Equals Target](./subset-sum-equals-target.md)
- [Partition Equal Subset Sum](./partition-equal-subset-sum.md)

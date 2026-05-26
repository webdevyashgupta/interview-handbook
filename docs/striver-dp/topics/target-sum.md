# Target Sum (DP on Subsequences)

## 🧩 Problem Summary
- **Problem:** Given an array of integers `arr` and a `target`, assign a `+` or `-` sign to each integer and sum them up. Find the number of ways to assign signs such that the sum equals the `target`.
- **Input:** Array `arr`, integer `target`.
- **Output:** Total number of ways to achieve the `target`.

## 💡 Intuition
- Assigning `+` and `-` signs is equivalent to partitioning the array into two subsets, $S_1$ and $S_2$, where:
  - $S_1$ is the set of elements with a `+` sign.
  - $S_2$ is the set of elements with a `-` sign.
- The equation becomes: $Sum(S_1) - Sum(S_2) = \text{target}$.
- We also know: $Sum(S_1) + Sum(S_2) = \text{TotalSum}$.
- Adding these two equations: $2 \times Sum(S_1) = \text{TotalSum} + \text{target}$.
- Therefore, $Sum(S_1) = \frac{\text{TotalSum} + \text{target}}{2}$.
- The problem reduces to: **Count the number of subsets with sum $K$**, where $K = \frac{\text{TotalSum} + \text{target}}{2}$.

## 🔁 Approach

### 1. Reducing the Problem
- Check if $(\text{TotalSum} + \text{target})$ is even and if $\text{TotalSum} \ge \text{target}$. If not, return 0.
- Let `new_target = (TotalSum + target) // 2`.
- Solve "Count Subsets with Sum `new_target`".

### 2. Tabulation (Count Subsets with Sum K)
- **Code (Python):**
```python
def count_subsets_with_sum_k(arr, k):
    n = len(arr)
    dp = [[0 for _ in range(k + 1)] for _ in range(n)]
    
    # Base Case: Sum 0
    if arr[0] == 0: dp[0][0] = 2 # Pick 0 or Not Pick 0
    else: dp[0][0] = 1 # Not Pick
    
    # Base Case: First element
    if arr[0] != 0 and arr[0] <= k:
        dp[0][arr[0]] = 1
        
    for i in range(1, n):
        for target in range(k + 1):
            not_pick = dp[i-1][target]
            pick = 0
            if arr[i] <= target:
                pick = dp[i-1][target - arr[i]]
            dp[i][target] = pick + not_pick
            
    return dp[n-1][k]
```

### 3. Space Optimization
- **Code (Python):**
```python
def target_sum(arr, target):
    total_sum = sum(arr)
    if (total_sum + target) % 2 != 0 or total_sum < abs(target):
        return 0
    
    k = (total_sum + target) // 2
    n = len(arr)
    prev = [0] * (k + 1)
    
    if arr[0] == 0: prev[0] = 2
    else: prev[0] = 1
    
    if arr[0] != 0 and arr[0] <= k:
        prev[arr[0]] = 1
        
    for i in range(1, n):
        cur = [0] * (k + 1)
        for t in range(k + 1):
            not_pick = prev[t]
            pick = 0
            if arr[i] <= t:
                pick = prev[t - arr[i]]
            cur[t] = pick + not_pick
        prev = cur
    return prev[k]
```

## 🧠 DP State Definition
- `dp[i][j]` is the number of ways to form sum `j` using elements from index `0` to `i`.

## ⏱️ Complexity
- **Time Complexity:** $O(N \times K)$, where $K = \frac{\text{TotalSum} + \text{target}}{2}$.
- **Space Complexity:** $O(K)$ with space optimization.

## 📌 Pattern
- **DP on Subsequences:** Specifically, "Count Subsets with Given Sum" variation.

## ⚠️ Common Mistakes
- **Zeroes in Array:** If the array contains zeroes, the base case `dp[i][0]` is not simply `1`. A zero can be either picked or not picked, doubling the ways.
- **Edge Cases:** $(\text{TotalSum} + \text{target})$ must be non-negative and even.

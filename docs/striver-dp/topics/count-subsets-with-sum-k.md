# Count Subsets with Sum K (DP on Subsequences)

## 🧩 Problem Summary
- **Problem:** Given an array of non-negative integers and a target $K$, count the number of subsets whose sum equals $K$.
- **Input:** An array `arr` and an integer `K`.
- **Output:** Total number of subsets with sum $K$.

## 💡 Intuition
- **The Core Idea:** Similar to "Subset Sum Equals Target", but instead of checking for existence (OR), we sum the number of ways (Addition).
- **Pick/Not-Pick:**
  1. **Not Pick:** The number of ways remains the same as for the previous index with the same target.
  2. **Pick:** If `arr[index] <= target`, the number of ways is what we had for the previous index with `target - arr[index]`.
- **Total Ways:** `ways = count(index-1, target) + count(index-1, target - arr[index])`.

## 🔁 Approach

### 1. Recursion with Memoization
- **Explanation:** Use a 2D `dp` table to store the number of ways for each `(index, target)` state.
- **Code (Python):**
```python
def solve(index, target, arr, dp):
    if target == 0:
        return 1
    if index == 0:
        return 1 if arr[0] == target else 0
    
    if dp[index][target] != -1:
        return dp[index][target]
    
    not_pick = solve(index - 1, target, arr, dp)
    pick = 0
    if arr[index] <= target:
        pick = solve(index - 1, target - arr[index], arr, dp)
        
    dp[index][target] = pick + not_pick
    return dp[index][target]
```

### 2. Tabulation
- **Explanation:** Build the `dp` table bottom-up.
- **Code (Python):**
```python
def findWays(arr, k):
    n = len(arr)
    dp = [[0 for _ in range(k + 1)] for _ in range(n)]
    
    for i in range(n):
        dp[i][0] = 1
        
    if arr[0] <= k:
        dp[0][arr[0]] = 1
        
    for i in range(1, n):
        for target in range(1, k + 1):
            not_pick = dp[i-1][target]
            pick = 0
            if arr[i] <= target:
                pick = dp[i-1][target - arr[i]]
            dp[i][target] = pick + not_pick
            
    return dp[n-1][k]
```

### 3. Space Optimization
- **Explanation:** Use a single array `prev` to store results of the previous row.
- **Code (Python):**
```python
def findWays(arr, k):
    n = len(arr)
    prev = [0] * (k + 1)
    prev[0] = 1
    
    if arr[0] <= k:
        prev[arr[0]] = 1
        
    for i in range(1, n):
        cur = [0] * (k + 1)
        cur[0] = 1
        for target in range(1, k + 1):
            not_pick = prev[target]
            pick = 0
            if arr[i] <= target:
                pick = prev[target - arr[i]]
            cur[target] = pick + not_pick
        prev = cur
            
    return prev[k]
```

## 🧠 Handling Zeros
- **The Problem:** If the array contains zeros, the base case `if target == 0 return 1` is incomplete because picking a zero doesn't change the sum but creates a new subset.
- **Modified Base Case (Recursion):**
```python
def solve(index, target, arr, dp):
    if index == 0:
        if target == 0 and arr[0] == 0:
            return 2  # Pick 0 or Not-pick 0
        if target == 0 or target == arr[0]:
            return 1
        return 0
    # ... rest of the code
```

## ⏱️ Complexity
- **Time Complexity:** $O(N \times K)$
- **Space Complexity:** $O(N \times K)$ for Tabulation, $O(K)$ for Space Optimization.

## 📌 Pattern
- **DP on Subsequences:** Counting combinations using addition.

## ⚠️ Common Mistakes
- **Initialising dp[i][0]:** If elements can be zero, `dp[i][0]` is not simply 1. Use the modified base case logic.
- **Large Outputs:** Often these problems require returning the result modulo $10^9 + 7$.

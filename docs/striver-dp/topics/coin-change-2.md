# Coin Change 2 (DP on Subsequences)

## 🧩 Problem Summary
- **Problem:** Given an array of coin denominations `coins` and a `target` amount, find the number of ways to make the change for the `target`. Each coin can be used an **infinite** number of times.
- **Input:** Array `coins`, integer `target`.
- **Output:** Total number of combinations that make up the `target`.

## 💡 Intuition
- This is a variation of the "Count Subsets with Sum K" problem, but with **infinite supply**.
- **Pick/Not-Pick:**
  1. **Not Pick:** Move to the previous index: `f(index - 1, target)`.
  2. **Pick:** Since we have infinite supply, stay at the **same index** and reduce the target: `f(index, target - coins[index])`.
- Base Case: At `index 0`, if the `target` is divisible by `coins[0]`, return 1 (there is one way: pick `coins[0]` multiple times). Else, return 0.

## 🔁 Approach

### 1. Recursion with Memoization
- **Explanation:** Use a 2D `dp` table of size `n x (target + 1)`.
- **Code (Python):**
```python
def solve(index, target, coins, dp):
    if index == 0:
        return 1 if target % coins[0] == 0 else 0
    
    if dp[index][target] != -1:
        return dp[index][target]
    
    not_pick = solve(index - 1, target, coins, dp)
    pick = 0
    if coins[index] <= target:
        pick = solve(index, target - coins[index], coins, dp)
        
    dp[index][target] = pick + not_pick
    return dp[index][target]
```

### 2. Tabulation
- **Code (Python):**
```python
def count_ways(n, target, coins):
    dp = [[0 for _ in range(target + 1)] for _ in range(n)]
    
    # Base case
    for t in range(target + 1):
        if t % coins[0] == 0:
            dp[0][t] = 1
            
    for i in range(1, n):
        for t in range(target + 1):
            not_pick = dp[i-1][t]
            pick = 0
            if coins[i] <= t:
                pick = dp[i][t - coins[i]]
            dp[i][t] = pick + not_pick
            
    return dp[n-1][target]
```

### 3. Space Optimization (Single Row)
- **Explanation:** To calculate `dp[i][t]`, we need `dp[i-1][t]` (same column, previous row) and `dp[i][t - coins[i]]` (previous column, same row). Because we need the *updated* value from the same row, we should iterate **forwards**.
- **Code (Python):**
```python
def count_ways(n, target, coins):
    prev = [0] * (target + 1)
    for t in range(target + 1):
        if t % coins[0] == 0:
            prev[t] = 1
            
    for i in range(1, n):
        for t in range(target + 1):
            not_pick = prev[t]
            pick = 0
            if coins[i] <= t:
                pick = prev[t - coins[i]] # Using updated 'prev' for same row
            prev[t] = pick + not_pick
            
    return prev[target]
```

## 🧠 DP State Definition
- `dp[i][t]` is the number of ways to form sum `t` using coins from index `0` to `i`.

## ⏱️ Complexity
- **Time Complexity:** $O(N \times Target)$
- **Space Complexity:** $O(Target)$ with space optimization.

## 📌 Pattern
- **Infinite Supply Pattern:** When an element can be reused, stay at the same index during the "Pick" step.

## ⚠️ Common Mistakes
- **Forwards vs Backwards Iteration:** In 0/1 Knapsack (finite supply), we iterate backwards. In Unbounded Knapsack/Coin Change (infinite supply), we iterate forwards.

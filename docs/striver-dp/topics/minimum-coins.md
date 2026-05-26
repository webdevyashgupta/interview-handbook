# Minimum Coins (DP on Subsequences - Infinite Supply)

## 🧩 Problem Summary
- **Problem:** Given an array of coin denominations and a target amount `T`, find the minimum number of coins required to form the target sum. You have an infinite supply of each coin.
- **Input:** Array `coins`, target `T`.
- **Output:** Minimum number of coins. If it's impossible to form the target, return -1 (or a large value).

## 💡 Intuition
- **Infinite Supply:** Unlike 0/1 problems, if you "pick" a coin, you stay at the same index because you can pick it again. If you "not pick", you move to the previous index.
- **Why Greedy Fails:** For denominations like $\{9, 6, 5, 1\}$ and target $11$, greedy would pick $9 + 1 + 1$ (3 coins), but the optimal is $6 + 5$ (2 coins). Greedy only works when denominations are canonical (like US currency).
- **Pick/Not-Pick:**
  1. **Not Pick:** `0 + f(index - 1, target)`
  2. **Pick:** `1 + f(index, target - coins[index])` (only if `coins[index] <= target`)

## 🔁 Approach

### 1. Recursion with Memoization
- **Explanation:** Use a 2D `dp` table of size `n x (T + 1)`. Initialize with -1.
- **Code (Python):**
```python
def solve(index, target, coins, dp):
    # Base case
    if index == 0:
        if target % coins[0] == 0:
            return target // coins[0]
        return int(1e9)
    
    if dp[index][target] != -1:
        return dp[index][target]
    
    not_pick = 0 + solve(index - 1, target, coins, dp)
    pick = int(1e9)
    if coins[index] <= target:
        pick = 1 + solve(index, target - coins[index], coins, dp)
        
    dp[index][target] = min(pick, not_pick)
    return dp[index][target]
```

### 2. Tabulation
- **Explanation:** Build the table bottom-up.
- **Code (Python):**
```python
def minCoins(coins, t):
    n = len(coins)
    dp = [[0 for _ in range(t + 1)] for _ in range(n)]
    
    # Base case for index 0
    for target in range(t + 1):
        if target % coins[0] == 0:
            dp[0][target] = target // coins[0]
        else:
            dp[0][target] = int(1e9)
            
    for i in range(1, n):
        for target in range(t + 1):
            not_pick = dp[i-1][target]
            pick = int(1e9)
            if coins[i] <= target:
                pick = 1 + dp[i][target - coins[i]]
            dp[i][target] = min(pick, not_pick)
            
    ans = dp[n-1][t]
    return ans if ans < int(1e9) else -1
```

### 3. Space Optimization
- **Explanation:** Since each row depends on its own values (due to infinite supply) and the previous row, we can optimize to one row.
- **Code (Python):**
```python
def minCoins(coins, t):
    n = len(coins)
    prev = [0] * (t + 1)
    
    for target in range(t + 1):
        if target % coins[0] == 0:
            prev[target] = target // coins[0]
        else:
            prev[target] = int(1e9)
            
    for i in range(1, n):
        cur = [0] * (t + 1)
        for target in range(t + 1):
            not_pick = prev[target]
            pick = int(1e9)
            if coins[i] <= target:
                pick = 1 + cur[target - coins[i]] # Note: using 'cur' here for infinite supply
            cur[target] = min(pick, not_pick)
        prev = cur
        
    ans = prev[t]
    return ans if ans < int(1e9) else -1
```

## 🧠 DP State Definition
- `dp[i][T]` is the minimum number of coins needed to form target `T` using coins from index `0` to `i`.

## 🔄 Recurrence Relation
- `f(i, T) = min(f(i-1, T), 1 + f(i, T - coins[i]))`

## ⏱️ Complexity
- **Time Complexity:** $O(N \times T)$
- **Space Complexity:** $O(N \times T)$ for Memoization/Tabulation, $O(T)$ for Space Optimization.

## 📌 Pattern
- **Unbounded Knapsack / Infinite Supply:** When you can pick an item multiple times, the "pick" transition stays at the same index `i`.

## ⚠️ Common Mistakes
- **Base Case:** Forgetting that if `target % coins[0] != 0`, it's impossible (return infinity).
- **Pick Transition:** Moving to `index - 1` when picking a coin. In infinite supply problems, you must stay at `index`.
- **Large Value:** Use a sufficiently large value like $10^9$ for infinity, but not so large that it causes overflow when adding 1.

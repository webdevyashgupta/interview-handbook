# Unbounded Knapsack (DP on Subsequences)

## 🧩 Problem Summary
- **Problem:** Given $N$ items with their weights `wt` and values `val`, and a bag of capacity `W`. You can pick each item an **infinite** number of times. Maximize the total value in the bag without exceeding capacity `W`.
- **Input:** Array `wt`, Array `val`, integer `W`.
- **Output:** Maximum total value.

## 💡 Intuition
- This is exactly like 0/1 Knapsack, but with **infinite supply**.
- **Pick/Not-Pick:**
  1. **Not Pick:** Move to the previous item: `f(index - 1, cap)`.
  2. **Pick:** Stay at the **same index** and reduce capacity: `val[index] + f(index, cap - wt[index])`.
- Base Case: At `index 0`, you can pick the item as many times as it fits: `(capacity // wt[0]) * val[0]`.

## 🔁 Approach

### 1. Recursion with Memoization
- **Code (Python):**
```python
def solve(index, cap, wt, val, dp):
    if index == 0:
        return (cap // wt[0]) * val[0]
    
    if dp[index][cap] != -1:
        return dp[index][cap]
    
    not_pick = solve(index - 1, cap, wt, val, dp)
    pick = -1e9
    if wt[index] <= cap:
        pick = val[index] + solve(index, cap - wt[index], wt, val, dp)
        
    dp[index][cap] = max(pick, not_pick)
    return dp[index][cap]
```

### 2. Tabulation
- **Code (Python):**
```python
def unbounded_knapsack(n, w, wt, val):
    dp = [[0 for _ in range(w + 1)] for _ in range(n)]
    
    for cap in range(w + 1):
        dp[0][cap] = (cap // wt[0]) * val[0]
        
    for i in range(1, n):
        for cap in range(w + 1):
            not_pick = dp[i-1][cap]
            pick = -1e9
            if wt[i] <= cap:
                pick = val[i] + dp[i][cap - wt[i]]
            dp[i][cap] = max(pick, not_pick)
            
    return dp[n-1][w]
```

### 3. Space Optimization (Single Row)
- **Explanation:** To calculate `dp[i][cap]`, we need `dp[i-1][cap]` (previous row) and `dp[i][cap - wt[i]]` (same row). By iterating **forwards**, we use the updated value of the current row, which naturally handles the infinite supply.
- **Code (Python):**
```python
def unbounded_knapsack(n, w, wt, val):
    prev = [0] * (w + 1)
    for cap in range(w + 1):
        prev[cap] = (cap // wt[0]) * val[0]
        
    for i in range(1, n):
        for cap in range(w + 1):
            not_pick = prev[cap]
            pick = -1e9
            if wt[i] <= cap:
                pick = val[i] + prev[cap - wt[i]]
            prev[cap] = max(pick, not_pick)
            
    return prev[w]
```

## 🧠 DP State Definition
- `dp[i][cap]` is the maximum value using items `0` to `i` with capacity `cap`.

## ⏱️ Complexity
- **Time Complexity:** $O(N \times W)$
- **Space Complexity:** $O(W)$ with single row optimization.

## 📌 Pattern
- **Infinite Supply:** Whenever you can reuse an item, the "pick" transition stays at the current index.

## ⚠️ Common Mistakes
- **Iteration Order:** 
  - 0/1 Knapsack (Finite) -> Backwards iteration.
  - Unbounded Knapsack (Infinite) -> Forwards iteration.

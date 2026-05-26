# 0/1 Knapsack (DP on Subsequences)

## 🧩 Problem Summary
- **Problem:** A thief wants to steal items from a house. There are $N$ items, each with a `weight` and a `value`. The thief has a bag with a maximum capacity `W`. The goal is to maximize the total value of items in the bag without exceeding the capacity.
- **Constraints:** 0/1 means you can either take an item (1) or leave it (0). You cannot take fractional items.
- **Input:** Weights array `wt`, Values array `val`, number of items `n`, and capacity `W`.
- **Output:** Maximum value.

## 💡 Intuition
- **Pick/Not-Pick:** For every item, the thief has two choices:
  1. **Not Pick:** The value is the same as what could be obtained from the remaining items with the same bag capacity.
  2. **Pick:** If the item's weight is less than or equal to the current capacity, add its value to the result of picking from remaining items with reduced capacity.
- **Recursive Relation:** `f(index, capacity) = max(not_pick, pick)`.

## 🔁 Approach

### 1. Recursion with Memoization
- **Explanation:** Use a 2D `dp` table of size `n x (W + 1)`.
- **Code (Python):**
```python
def solve(index, capacity, wt, val, dp):
    if index == 0:
        if wt[0] <= capacity:
            return val[0]
        return 0
    
    if dp[index][capacity] != -1:
        return dp[index][capacity]
    
    not_pick = solve(index - 1, capacity, wt, val, dp)
    pick = -1e9
    if wt[index] <= capacity:
        pick = val[index] + solve(index - 1, capacity - wt[index], wt, val, dp)
        
    dp[index][capacity] = max(pick, not_pick)
    return dp[index][capacity]
```

### 2. Tabulation
- **Explanation:** Build the table bottom-up from base cases.
- **Code (Python):**
```python
def knapsack(n, w, wt, val):
    dp = [[0 for _ in range(w + 1)] for _ in range(n)]
    
    # Base case: for index 0, if weight <= capacity, we take it
    for capacity in range(wt[0], w + 1):
        dp[0][capacity] = val[0]
        
    for i in range(1, n):
        for capacity in range(w + 1):
            not_pick = dp[i-1][capacity]
            pick = -1e9
            if wt[i] <= capacity:
                pick = val[i] + dp[i-1][capacity - wt[i]]
            dp[i][capacity] = max(pick, not_pick)
            
    return dp[n-1][w]
```

### 3. Space Optimization (Two Rows)
- **Explanation:** Use `prev` and `cur` arrays.
- **Code (Python):**
```python
def knapsack(n, w, wt, val):
    prev = [0] * (w + 1)
    for capacity in range(wt[0], w + 1):
        prev[capacity] = val[0]
        
    for i in range(1, n):
        cur = [0] * (w + 1)
        for capacity in range(w + 1):
            not_pick = prev[capacity]
            pick = -1e9
            if wt[i] <= capacity:
                pick = val[i] + prev[capacity - wt[i]]
            cur[capacity] = max(pick, not_pick)
        prev = cur
    return prev[w]
```

### 4. Ultimate Space Optimization (Single Row)
- **Explanation:** Notice that when calculating `cur[capacity]`, we only need values from `prev` at indices `capacity` and `capacity - wt[i]`. If we iterate `capacity` **backwards** from `W` to `0`, we can update the same array without overwriting values needed for the same row.
- **Code (Python):**
```python
def knapsack(n, w, wt, val):
    prev = [0] * (w + 1)
    for capacity in range(wt[0], w + 1):
        prev[capacity] = val[0]
        
    for i in range(1, n):
        # Iterate backwards to use only one array
        for capacity in range(w, -1, -1):
            not_pick = prev[capacity]
            pick = -1e9
            if wt[i] <= capacity:
                pick = val[i] + prev[capacity - wt[i]]
            prev[capacity] = max(pick, not_pick)
            
    return prev[w]
```

## 🧠 DP State Definition
- `dp[i][w]` is the maximum value that can be obtained using items from index `0` to `i` with a bag capacity of `w`.

## ⏱️ Complexity
- **Time Complexity:** $O(N \times W)$
- **Space Complexity:**
  - $O(N \times W)$ for Memoization/Tabulation.
  - $O(W)$ for Space Optimization (Single Row).

## 📌 Pattern
- **DP on Subsequences:** The classic 0/1 Knapsack pattern.

## ⚠️ Common Mistakes
- **Base Case:** Forgetting that at `index 0`, you can take the item for *all* capacities $\ge wt[0]$, not just exactly $wt[0]$.
- **Backwards Iteration:** In the single-row optimization, if you iterate forwards, you will accidentally use the updated value (representing "pick same item multiple times"), which converts the problem into Unbounded Knapsack.

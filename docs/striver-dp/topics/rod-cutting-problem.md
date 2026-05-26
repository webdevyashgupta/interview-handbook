# Rod Cutting Problem (DP on Subsequences)

## 🧩 Problem Summary
- **Problem:** Given a rod of length $N$ and an array `price` where `price[i]` is the price of a rod of length $i+1$. Cut the rod into smaller pieces to maximize the total value.
- **Input:** Integer `N`, Array `price`.
- **Output:** Maximum total value.

## 💡 Intuition
- This is a classic **Unbounded Knapsack** problem.
- **Mapping to Knapsack:**
  - Bag Capacity = Rod length `N`.
  - Item Weight = Length of the piece ($1, 2, \dots, N$).
  - Item Value = `price[length - 1]`.
  - Each length can be used multiple times (Infinite Supply).
- **Pick/Not-Pick:**
  1. **Not Pick:** Try making pieces using lengths only up to `index - 1`.
  2. **Pick:** Take a piece of current `length` and stay at the `index` (since you can take another piece of the same length).

## 🔁 Approach

### 1. Recursion with Memoization
- **Code (Python):**
```python
def solve(index, current_n, price, dp):
    if index == 0:
        # At index 0, piece length is 1. We take current_n pieces.
        return current_n * price[0]
    
    if dp[index][current_n] != -1:
        return dp[index][current_n]
    
    not_pick = solve(index - 1, current_n, price, dp)
    pick = -1e9
    rod_length = index + 1
    if rod_length <= current_n:
        pick = price[index] + solve(index, current_n - rod_length, price, dp)
        
    dp[index][current_n] = max(pick, not_pick)
    return dp[index][current_n]
```

### 2. Tabulation
- **Code (Python):**
```python
def cut_rod(n, price):
    dp = [[0 for _ in range(n + 1)] for _ in range(n)]
    
    for length in range(n + 1):
        dp[0][length] = length * price[0]
        
    for i in range(1, n):
        for length in range(n + 1):
            not_pick = dp[i-1][length]
            pick = -1e9
            rod_len = i + 1
            if rod_len <= length:
                pick = price[i] + dp[i][length - rod_len]
            dp[i][length] = max(pick, not_pick)
            
    return dp[n-1][n]
```

### 3. Space Optimization (Single Row)
- **Code (Python):**
```python
def cut_rod(n, price):
    prev = [0] * (n + 1)
    
    for length in range(n + 1):
        prev[length] = length * price[0]
        
    for i in range(1, n):
        # Forward iteration for infinite supply
        for length in range(n + 1):
            not_pick = prev[length]
            pick = -1e9
            rod_len = i + 1
            if rod_len <= length:
                pick = price[i] + prev[length - rod_len]
            prev[length] = max(pick, not_pick)
            
    return prev[n]
```

## 🧠 DP State Definition
- `dp[i][L]` is the maximum price obtainable using pieces of lengths up to `i+1` for a rod of length `L`.

## ⏱️ Complexity
- **Time Complexity:** $O(N \times N)$
- **Space Complexity:** $O(N)$ with single row optimization.

## 📌 Pattern
- **Unbounded Knapsack Pattern:** Items can be reused; capacity is the total rod length.

## ⚠️ Common Mistakes
- **Indexing:** Remember that the length of the piece at `index` is `index + 1`.
- **Base Case:** At `index 0`, the piece length is `1`, so the maximum value is `total_length * price_of_length_1`.

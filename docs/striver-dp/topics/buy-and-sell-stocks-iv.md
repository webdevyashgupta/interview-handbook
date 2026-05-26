# Best Time to Buy and Sell Stock IV

## 🧩 Problem Summary
You are given an array `prices` where `prices[i]` is the price of a given stock on the $i^{th}$ day, and an integer `k`. You can perform **at most k** transactions.

### Input/Output
- **Input**: `k = 2, prices = [2, 4, 1]`
- **Output**: `2` (Buy at 2, sell at 4)

### Constraints
- $0 \le k \le 100$
- $0 \le prices.length \le 1000$

## 💡 Intuition
This is the generalized version of Stock III. Instead of a fixed capacity of 2, we use a variable capacity `k`.
The state can be represented in two ways:
1. `(index, buy, capacity)`: where `capacity` goes from `k` down to `0`. (3D DP)
2. `(index, transactionNumber)`: where `transactionNumber` goes from `0` to `2*k - 1`. (2D DP)
   - Even `transactionNumber`: Buy
   - Odd `transactionNumber`: Sell

## 🔁 Approach

### 1. Recursion (Capacity method)
`f(ind, buy, cap)`:
- Similar to Stock III, but initialize `cap = k`.

### 2. Recursion (Transaction Number method)
`f(ind, tranNo)`:
- If `tranNo % 2 == 0` (Buy): `max(-prices[ind] + f(ind+1, tranNo+1), f(ind+1, tranNo))`
- If `tranNo % 2 == 1` (Sell): `max(prices[ind] + f(ind+1, tranNo+1), f(ind+1, tranNo))`

### 3. Memoization
- Capacity method: `dp[n][2][k+1]`
- Transaction method: `dp[n][2*k]`

### 4. Tabulation
Convert the recursive calls to iterative loops. For the transaction method, the state `dp[ind][tranNo]` only depends on `dp[ind+1]`.

### 5. Space Optimization
Use two rows: `after` and `curr`.
- Capacity method: `after[2][k+1]`
- Transaction method: `after[2*k + 1]`

## 🧠 DP State Definition
**Transaction Method**: `dp[ind][tranNo]` is the maximum profit starting from day `ind` with `tranNo` transactions already initiated.

## 🔄 Recurrence Relation
**Transaction Method**:
```cpp
if (tranNo % 2 == 0) // Buy
    dp[i][tranNo] = max(-prices[i] + dp[i+1][tranNo+1], dp[i+1][tranNo]);
else // Sell
    dp[i][tranNo] = max(prices[i] + dp[i+1][tranNo+1], dp[i+1][tranNo]);
```

## ⏱️ Complexity (Time & Space)
- **Time**: $O(N \times K)$
- **Space**: $O(K)$ with space optimization.

## 📌 Pattern
Stock DP (K Transactions)

## ⚠️ Common Mistakes
- **Capacity**: If using the transaction number method, remember that `k` transactions mean `2*k` stages (buy and sell).
- **Index Out of Bounds**: When using `tranNo + 1`, ensure the DP table size is `2*k + 1`.

## 🔗 Related Problems
- Best Time to Buy and Sell Stock III
- Best Time to Buy and Sell Stock with Cooldown
- Best Time to Buy and Sell Stock with Transaction Fee

# Best Time to Buy and Sell Stock II

## 🧩 Problem Summary
You are given an array `prices` where `prices[i]` is the price of a given stock on the $i^{th}$ day. You can perform **unlimited** transactions (buy and sell multiple times), but you must sell the stock before you can buy again.

### Input/Output
- **Input**: `prices = [7, 1, 5, 3, 6, 4]`
- **Output**: `7` (Buy at 1, sell at 5 [profit 4]; buy at 3, sell at 6 [profit 3]; Total = 7)

### Constraints
- $1 \le prices.length \le 3 \times 10^4$
- $0 \le prices[i] \le 10^4$

## 💡 Intuition
Since we can perform multiple transactions, we want to capture every price increase. 
- On any given day, we have two states: we either **own a stock** or we **don't**.
- If we don't own a stock, we can either buy today or skip.
- If we own a stock, we can either sell today or skip.
This "Choice" leads to a recursive structure.

## 🔁 Approach

### 1. Recursion
Define `f(ind, buy)`:
- `buy = 1`: Can buy. Profit = `max(-prices[ind] + f(ind+1, 0), f(ind+1, 1))`
- `buy = 0`: Can sell. Profit = `max(prices[ind] + f(ind+1, 1), f(ind+1, 0))`

### 2. Memoization (Top-Down DP)
Use a 2D array `dp[n][2]` to store results of `f(ind, buy)`.

### 3. Tabulation (Bottom-Up DP)
Iterate from `n-1` down to `0`. 
Base case: `dp[n][0] = dp[n][1] = 0`.

### 4. Space Optimization
Since `dp[ind]` only depends on `dp[ind+1]`, we can use two variables (or an array of size 2) for the "ahead" state and the "current" state.

## 🧠 DP State Definition
`dp[ind][buy]` is the maximum profit we can make from day `ind` onwards, given our current status `buy` (1 if we can buy, 0 if we must sell).

## 🔄 Recurrence Relation
- **Buying state**: `profit = max(-prices[ind] + dp[ind+1][0], 0 + dp[ind+1][1])`
- **Selling state**: `profit = max(prices[ind] + dp[ind+1][1], 0 + dp[ind+1][0])`

## ⏱️ Complexity (Time & Space)
- **Time**: $O(N)$
- **Space**: $O(1)$ with space optimization (or $O(N)$ for memoization/tabulation).

## 📌 Pattern
Stock DP (Infinite Transactions)

## ⚠️ Common Mistakes
- **Overlapping Transactions**: You cannot buy again if you already own a stock. The `buy` variable correctly tracks this.
- **Initialization**: Forgetting to initialize the DP table with -1 (memoization).

## 🔗 Related Problems
- Best Time to Buy and Sell Stock III
- Best Time to Buy and Sell Stock with Cooldown
- Best Time to Buy and Sell Stock with Transaction Fee

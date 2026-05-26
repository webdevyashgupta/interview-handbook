# Best Time to Buy and Sell Stock III

## 🧩 Problem Summary
You are given an array `prices` where `prices[i]` is the price of a given stock on the $i^{th}$ day. You can perform **at most two** transactions. You must sell the stock before you can buy again.

### Input/Output
- **Input**: `prices = [3, 3, 5, 0, 0, 3, 1, 4]`
- **Output**: `6` (Buy at 0, sell at 3 [profit 3]; buy at 1, sell at 4 [profit 3]; Total = 6)

### Constraints
- $1 \le prices.length \le 10^5$
- $0 \le prices[i] \le 10^5$

## 💡 Intuition
This is an extension of the infinite transactions problem, but now we have a **limit** on the number of transactions. 
- A transaction is completed when a **Buy followed by a Sell** occurs.
- We need a third state in our DP: `cap`, representing the number of transactions remaining.
- When we sell, we decrease `cap` by 1.

Alternatively, we can think of it as 4 stages: `Buy1, Sell1, Buy2, Sell2`.

## 🔁 Approach

### 1. Recursion
Define `f(ind, buy, cap)`:
- `buy = 1, cap > 0`: `max(-prices[ind] + f(ind+1, 0, cap), f(ind+1, 1, cap))`
- `buy = 0, cap > 0`: `max(prices[ind] + f(ind+1, 1, cap-1), f(ind+1, 0, cap))`
- Base Case: `if (ind == n || cap == 0) return 0;`

### 2. Memoization (Top-Down DP)
Use a 3D array `dp[n][2][3]` (2 for buy/sell, 3 for 0, 1, 2 remaining transactions).

### 3. Tabulation (Bottom-Up DP)
3D table iterating `ind` from `n-1` to `0`, `buy` from `0` to `1`, and `cap` from `1` to `2`.

### 4. Space Optimization
Since `dp[ind]` only depends on `dp[ind+1]`, we can reduce the 3D DP to two 2D arrays (`after[2][3]` and `curr[2][3]`).

## 🧠 DP State Definition
`dp[ind][buy][cap]` is the maximum profit we can make from day `ind` onwards, given our current status `buy` and remaining transaction capacity `cap`.

## 🔄 Recurrence Relation
- **If can buy**: `dp[i][1][cap] = max(-prices[i] + dp[i+1][0][cap], dp[i+1][1][cap])`
- **If can sell**: `dp[i][0][cap] = max(prices[i] + dp[i+1][1][cap-1], dp[i+1][0][cap])`

## ⏱️ Complexity (Time & Space)
- **Time**: $O(N \times 2 \times 3) = O(N)$
- **Space**: $O(1)$ with space optimization (fixed size array `2x3`).

## 📌 Pattern
Stock DP (K Transactions)

## ⚠️ Common Mistakes
- **Transaction count**: Only decrement the capacity when a **full** transaction (buy+sell) is completed. In the video's approach, `cap` is decremented at the Sell step.
- **Base cases**: Forgetting to handle `cap == 0`.

## 🔗 Related Problems
- Best Time to Buy and Sell Stock IV (At most K transactions)
- Best Time to Buy and Sell Stock II (Infinite transactions)

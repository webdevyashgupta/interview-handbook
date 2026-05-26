# Buy and Sell Stocks with Transaction Fee

## 🧩 Problem Summary
- **Problem:** You are given an array of stock prices and a transaction fee. You can buy and sell stocks unlimited times, but for every transaction (a buy and a sell together), a fixed `fee` is charged.
- **Goal:** Maximize the total net profit.
- **Constraints:** You cannot buy a stock if you already hold one. You must sell the current one before buying again.

## 💡 Intuition
- This problem is an extension of "Buy and Sell Stocks II" (unlimited transactions).
- The core logic remains: on any day, you can either buy/sell or skip.
- To handle the fee, we subtract it exactly once per transaction. This can be done either when we perform a **buy** operation or when we perform a **sell** operation.

## 🔁 Approach

### 1. Recursion with Memoization
- **Explanation:** Similar to Stock II, but subtract `fee` during either the buy or sell step.
- **Code (C++):**
```cpp
int solve(int index, int buy, vector<int>& prices, int fee, vector<vector<int>>& dp) {
    if (index == prices.size()) return 0;
    
    if (dp[index][buy] != -1) return dp[index][buy];
    
    if (buy) {
        // Buy (subtract price) or Skip
        return dp[index][buy] = max(-prices[index] + solve(index + 1, 0, prices, fee, dp),
                                   0 + solve(index + 1, 1, prices, fee, dp));
    } else {
        // Sell (add price AND subtract fee) or Skip
        return dp[index][buy] = max(prices[index] - fee + solve(index + 1, 1, prices, fee, dp),
                                   0 + solve(index + 1, 0, prices, fee, dp));
    }
}
```

### 2. Tabulation
- **Explanation:** Build the DP table bottom-up.
- **Code (C++):**
```cpp
int stockWithFee(vector<int>& prices, int fee) {
    int n = prices.size();
    vector<vector<int>> dp(n + 1, vector<int>(2, 0));
    
    for (int i = n - 1; i >= 0; i--) {
        // Buy case
        dp[i][1] = max(-prices[i] + dp[i + 1][0], 0 + dp[i + 1][1]);
        // Sell case
        dp[i][0] = max(prices[i] - fee + dp[i + 1][1], 0 + dp[i + 1][0]);
    }
    return dp[0][1];
}
```

### 3. Space Optimization
- **Explanation:** Only the next day's results are needed, so we can use two variables for `buy` and `sell` states.
- **Code (C++):**
```cpp
int stockWithFee(vector<int>& prices, int fee) {
    int n = prices.size();
    int nextBuy, nextSell, curBuy, curSell;
    nextBuy = nextSell = 0;
    
    for (int i = n - 1; i >= 0; i--) {
        curBuy = max(-prices[i] + nextSell, 0 + nextBuy);
        curSell = max(prices[i] - fee + nextBuy, 0 + nextSell);
        
        nextBuy = curBuy;
        nextSell = curSell;
    }
    return nextBuy;
}
```

## 🧠 DP State Definition
- `dp[i][buy]` is the maximum profit that can be obtained from day `i` onwards, given whether we are currently holding a stock (`buy=0`) or not (`buy=1`).

## ⏱️ Complexity
- **Time Complexity:** $O(N)$
- **Space Complexity:**
  - $O(N)$ for Memoization/Tabulation.
  - $O(1)$ for Space Optimization.

## 📌 Pattern
- **DP on Stocks:** Variation of unlimited transactions with a per-transaction cost.

## ⚠️ Common Mistakes
- **Double Counting Fee:** Ensure the fee is subtracted only once (either at buy or sell), not both.
- **Negative Profit:** If the fee is higher than any possible profit, the result should be 0 (no transactions).

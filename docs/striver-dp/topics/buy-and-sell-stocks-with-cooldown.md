# Buy and Sell Stocks with Cooldown

## 🧩 Problem Summary
- **Problem:** You are given an array of stock prices. You can buy and sell stocks unlimited times to maximize profit, but with a specific constraint: after selling a stock, you cannot buy again on the very next day. You must wait for at least one day (cooldown).
- **Goal:** Maximize the total profit.
- **Constraints:** After selling on day `i`, the next day you can buy is day `i + 2`.

## 💡 Intuition
- This is a variation of the "Buy and Sell Stocks II" problem (unlimited transactions).
- In Stock II, when we sell, we move to `index + 1` and are allowed to buy.
- In this problem, when we sell, we move to `index + 2` to account for the 1-day cooldown.
- For every day, we have two choices:
  1. **If we can buy:** Either buy today or skip.
  2. **If we can sell:** Either sell today or skip. If we sell, we must skip the next day (cooldown).

## 🔁 Approach

### 1. Recursion with Memoization
- **Explanation:** Use a 2D `dp` table of size `n x 2`. The `index` goes up to `n`, and `buy` is 0 or 1.
- **Code (C++):**
```cpp
int solve(int index, int buy, vector<int>& prices, vector<vector<int>>& dp) {
    if (index >= prices.size()) return 0;
    
    if (dp[index][buy] != -1) return dp[index][buy];
    
    if (buy) {
        // Buy or Skip
        return dp[index][buy] = max(-prices[index] + solve(index + 1, 0, prices, dp),
                                   0 + solve(index + 1, 1, prices, dp));
    } else {
        // Sell (move to index + 2) or Skip
        return dp[index][buy] = max(prices[index] + solve(index + 2, 1, prices, dp),
                                   0 + solve(index + 1, 0, prices, dp));
    }
}
```

### 2. Tabulation
- **Explanation:** Bottom-up approach. Note that since we access `index + 2`, the DP table should be of size `n + 2`.
- **Code (C++):**
```cpp
int stockCooldown(vector<int>& prices) {
    int n = prices.size();
    vector<vector<int>> dp(n + 2, vector<int>(2, 0));
    
    for (int i = n - 1; i >= 0; i--) {
        // Buy case
        dp[i][1] = max(-prices[i] + dp[i + 1][0], 0 + dp[i + 1][1]);
        // Sell case
        dp[i][0] = max(prices[i] + dp[i + 2][1], 0 + dp[i + 1][0]);
    }
    return dp[0][1];
}
```

### 3. Space Optimization
- **Explanation:** Instead of a full table, use three arrays (or variables) to represent `index`, `index + 1`, and `index + 2`.
- **Code (C++):**
```cpp
int stockCooldown(vector<int>& prices) {
    int n = prices.size();
    vector<int> front2(2, 0);
    vector<int> front1(2, 0);
    vector<int> cur(2, 0);
    
    for (int i = n - 1; i >= 0; i--) {
        cur[1] = max(-prices[i] + front1[0], 0 + front1[1]);
        cur[0] = max(prices[i] + front2[1], 0 + front1[0]);
        
        front2 = front1;
        front1 = cur;
    }
    return cur[1];
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
- **DP on Stocks:** Variation of unlimited transactions with a state-skipping constraint.

## ⚠️ Common Mistakes
- **Base Case:** When using `index + 2`, ensure the base case handles `index >= n`, not just `index == n`.
- **State Transition:** Remember that the cooldown only applies *after* a sell. You can buy any time you aren't holding a stock, unless you sold yesterday.

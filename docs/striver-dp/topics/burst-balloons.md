# Burst Balloons (Partition DP)

## 🧩 Problem Summary
- **Problem:** You are given `n` balloons, each with a number. If you burst balloon `i`, you get `nums[i-1] * nums[i] * nums[i+1]` coins. After bursting, the neighbors become adjacent. Find the maximum coins you can collect.
- **Constraints:** If `i-1` or `i+1` is out of bounds, treat it as a balloon with value `1`.
- **Example:** `nums = [3, 1, 5, 8]`
  - Burst 1: `3*1*5 = 15`. Remaining: `[3, 5, 8]`
  - Burst 5: `3*5*8 = 120`. Remaining: `[3, 8]`
  - Burst 3: `1*3*8 = 24`. Remaining: `[8]`
  - Burst 8: `1*8*1 = 8`.
  - Total: `15 + 120 + 24 + 8 = 167`.

## 💡 Intuition
- **The Challenge:** If we try to decide which balloon to burst *first*, the subproblems are dependent. Bursting a balloon changes the neighbors for the remaining balloons, making it impossible to solve left and right parts independently.
- **The Reverse Strategy:** Instead of "which balloon to burst first", think "which balloon is the **last** to burst in the range `[i, j]`".
- If balloon `k` is the last to burst in range `[i, j]`:
  1. All balloons from `i` to `k-1` and `k+1` to `j` are already gone.
  2. The neighbors of `k` at the moment of bursting will be `nums[i-1]` and `nums[j+1]`.
  3. Cost = `nums[i-1] * nums[k] * nums[j+1] + f(i, k-1) + f(k+1, j)`.

## 🔁 Approach

### 1. Recursion with Memoization
- **Explanation:** Add `1` to both ends of the array. `i` and `j` will represent the current range of balloons.
- **Code (C++):**
```cpp
int solve(int i, int j, vector<int>& nums, vector<vector<int>>& dp) {
    if (i > j) return 0;
    if (dp[i][j] != -1) return dp[i][j];

    int maxi = -1e9;
    for (int k = i; k <= j; k++) {
        int coins = nums[i - 1] * nums[k] * nums[j + 1] 
                    + solve(i, k - 1, nums, dp) 
                    + solve(k + 1, j, nums, dp);
        maxi = max(maxi, coins);
    }
    return dp[i][j] = maxi;
}

int maxCoins(vector<int>& nums) {
    int n = nums.size();
    nums.insert(nums.begin(), 1);
    nums.push_back(1);
    vector<vector<int>> dp(n + 1, vector<int>(n + 1, -1));
    return solve(1, n, nums, dp);
}
```

### 2. Tabulation
- **Explanation:** Iterate through the range length from 1 to `n`.
- **Code (C++):**
```cpp
int maxCoins(vector<int>& nums) {
    int n = nums.size();
    nums.insert(nums.begin(), 1);
    nums.push_back(1);
    vector<vector<int>> dp(n + 2, vector<int>(n + 2, 0));

    for (int i = n; i >= 1; i--) {
        for (int j = 1; j <= n; j++) {
            if (i > j) continue;
            int maxi = -1e9;
            for (int k = i; k <= j; k++) {
                int coins = nums[i - 1] * nums[k] * nums[j + 1] 
                            + dp[i][k - 1] + dp[k + 1][j];
                maxi = max(maxi, coins);
            }
            dp[i][j] = maxi;
        }
    }
    return dp[1][n];
}
```

## 🧠 DP State Definition
- `dp[i][j]` is the maximum coins obtained by bursting all balloons in the range `[i, j]`.

## ⏱️ Complexity
- **Time Complexity:** $O(N^3)$ (Two nested loops for `i` and `j`, and one internal loop for `k`).
- **Space Complexity:** $O(N^2)$ for the DP table.

## 📌 Pattern
- **Partition DP (Bottom-Up Logic):** Solving the "last step" first to ensure subproblem independence.

## ⚠️ Common Mistakes
- **Top-Down Logic:** Trying to pick the first balloon to burst. This fails because the subproblems remain dependent on neighbors outside their range that haven't been bursted yet.
- **Indices:** Be careful with the `i-1` and `j+1` indices after padding the array with `1`s.

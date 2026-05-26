# Minimum Cost to Cut the Stick

## 🧩 Problem Summary
- **Problem:** Given a stick of length `n` and an array `cuts` where `cuts[i]` denotes the position of a cut you need to make. The cost of a cut is the length of the stick being cut. Find the minimum total cost to make all the cuts.
- **Example:** Stick length `n = 7`, cuts = `[1, 3, 4, 5]`.
  - If you cut at 1 first: Cost = 7, stick is split into `[0, 1]` and `[1, 7]`.
  - Then cut at 3: Cost = 6 (length of `[1, 7]`), stick is split into `[1, 3]` and `[3, 7]`.
  - Total cost varies depending on the order of cuts.

## 💡 Intuition
- **Sorting:** To solve subproblems independently, we must sort the `cuts` array. If we make a cut at some position, we want the remaining cuts to be clearly divided into those on the left and those on the right.
- **Boundaries:** Add `0` and `n` to the `cuts` array to represent the boundaries of the stick. After sorting, the length of any segment between `cuts[i]` and `cuts[j]` is simply `cuts[j] - cuts[i]`.
- **Partition DP:** This is a classic "Matrix Chain Multiplication" style problem. For a range defined by indices `i` and `j`, we try every possible cut `k` between them and take the minimum.

## 🔁 Approach

### 1. Recursion with Memoization
- **Explanation:** `f(i, j)` returns the minimum cost to make all cuts between index `i` and `j` of the sorted `cuts` array.
- **Code (C++):**
```cpp
int solve(int i, int j, vector<int>& cuts, vector<vector<int>>& dp) {
    if (i + 1 >= j) return 0;
    if (dp[i][j] != -1) return dp[i][j];

    int mini = 1e9;
    for (int k = i + 1; k < j; k++) {
        int cost = cuts[j] - cuts[i] + solve(i, k, cuts, dp) + solve(k, j, cuts, dp);
        mini = min(mini, cost);
    }
    return dp[i][j] = mini;
}

int minCost(int n, vector<int>& cuts) {
    int c = cuts.size();
    cuts.push_back(0);
    cuts.push_back(n);
    sort(cuts.begin(), cuts.end());
    vector<vector<int>> dp(c + 2, vector<int>(c + 2, -1));
    return solve(0, c + 1, cuts, dp);
}
```

### 2. Tabulation
- **Explanation:** Build the DP table bottom-up. The length of the range (difference between `j` and `i`) increases.
- **Code (C++):**
```cpp
int minCost(int n, vector<int>& cuts) {
    int c = cuts.size();
    cuts.push_back(0);
    cuts.push_back(n);
    sort(cuts.begin(), cuts.end());
    
    vector<vector<int>> dp(c + 2, vector<int>(c + 2, 0));
    
    for (int i = c; i >= 0; i--) {
        for (int j = i + 1; j <= c + 1; j++) {
            if (i + 1 >= j) continue;
            int mini = 1e9;
            for (int k = i + 1; k < j; k++) {
                int cost = cuts[j] - cuts[i] + dp[i][k] + dp[k][j];
                mini = min(mini, cost);
            }
            dp[i][j] = mini;
        }
    }
    return dp[0][c + 1];
}
```

## 🧠 DP State Definition
- `dp[i][j]` is the minimum cost to make all cuts between index `i` and index `j` in the sorted `cuts` array (including boundaries `0` and `n`).

## ⏱️ Complexity
- **Time Complexity:** $O(M^3)$ where $M$ is the number of cuts ($M = c + 2$).
- **Space Complexity:** $O(M^2)$ for the DP table.

## 📌 Pattern
- **Partition DP:** Similar to Matrix Chain Multiplication. The problem is solved by trying all possible "last" or "first" cuts in a range and recursing on the resulting sub-segments.

## ⚠️ Common Mistakes
- **Forgetting to Sort:** The independence of subproblems relies on the cuts being in increasing order.
- **Boundaries:** Forgetting to add `0` and `n` to the cuts array makes it difficult to calculate the length of the current segment.
- **Base Case:** The base case is when there are no cuts to be made between `i` and `j` (i.e., `j == i + 1`).

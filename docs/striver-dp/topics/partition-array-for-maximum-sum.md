# Partition Array for Maximum Sum (Front Partition)

## 🧩 Problem Summary
- **Problem:** Given an integer array `arr` and an integer `k`, partition the array into (contiguous) subarrays of length at most `k`. After partitioning, each subarray has its values changed to become the maximum value of that subarray. Return the largest sum of the given array after partitioning.
- **Example:** `arr = [1, 15, 7, 9, 2, 5, 10], k = 3`
  - Partition: `[1, 15, 7], [9], [2, 5, 10]`
  - Max values: `[15, 15, 15], [9], [10, 10, 10]`
  - Sum: `45 + 9 + 30 = 84`.

## 💡 Intuition
- **Front Partitioning:** At each index `i`, we can start a subarray of length `len` where `1 <= len <= k`.
- **Local Decision:** If we decide to end the current subarray at index `j`, the sum contributed by this part is `(j - i + 1) * max(arr[i...j])`.
- **Remaining Problem:** We then recursively solve for the part of the array starting from index `j + 1`.
- **Max of All:** We try all possible lengths and take the maximum result.

## 🔁 Approach

### 1. Recursion with Memoization
- **Explanation:** `f(i)` represents the maximum sum we can get from the subarray starting at index `i`.
- **Code (C++):**
```cpp
int solve(int i, int n, int k, vector<int>& arr, vector<int>& dp) {
    if (i == n) return 0;
    if (dp[i] != -1) return dp[i];

    int maxVal = -1e9;
    int maxAns = -1e9;
    int len = 0;
    
    // Try all possible partition lengths from 1 to k
    for (int j = i; j < min(n, i + k); j++) {
        len++;
        maxVal = max(maxVal, arr[j]);
        int sum = (len * maxVal) + solve(j + 1, n, k, arr, dp);
        maxAns = max(maxAns, sum);
    }
    return dp[i] = maxAns;
}

int maxSumAfterPartitioning(vector<int>& arr, int k) {
    int n = arr.size();
    vector<int> dp(n, -1);
    return solve(0, n, k, arr, dp);
}
```

### 2. Tabulation
- **Explanation:** Iterate from `n-1` down to `0` to build the DP array.
- **Code (C++):**
```cpp
int maxSumAfterPartitioning(vector<int>& arr, int k) {
    int n = arr.size();
    vector<int> dp(n + 1, 0);

    for (int i = n - 1; i >= 0; i--) {
        int maxVal = -1e9;
        int maxAns = -1e9;
        int len = 0;
        for (int j = i; j < min(n, i + k); j++) {
            len++;
            maxVal = max(maxVal, arr[j]);
            int sum = (len * maxVal) + dp[j + 1];
            maxAns = max(maxAns, sum);
        }
        dp[i] = maxAns;
    }
    return dp[0];
}
```

## 🧠 DP State Definition
- `dp[i]` is the maximum sum that can be obtained from the subarray starting from index `i` to the end of the array.

## ⏱️ Complexity
- **Time Complexity:** $O(N \times K)$ where $N$ is the number of elements and $K$ is the maximum partition length.
- **Space Complexity:** $O(N)$ for the DP table and recursion stack.

## 📌 Pattern
- **Front Partition:** This is a common pattern for "Partitioning" problems where you decide the first chunk and recurse on the rest.

## ⚠️ Common Mistakes
- **Max Value Tracking:** Efficiently track the max value of the current subarray as you expand its length, rather than recalculating it with a separate loop.
- **Indices:** Be careful with the `min(n, i + k)` boundary to avoid going out of bounds.
- **Base Case:** The base case `i == n` should return `0` because no elements remain to form a sum.

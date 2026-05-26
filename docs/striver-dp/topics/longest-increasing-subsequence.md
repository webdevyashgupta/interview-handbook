# Longest Increasing Subsequence (LIS)

## 🧩 Problem Summary
- **Problem:** Given an array of $N$ integers, find the length of the longest subsequence such that all elements of the subsequence are sorted in strictly increasing order.
- **Subsequence:** A sequence that can be derived from another sequence by deleting zero or more elements without changing the order of the remaining elements.
- **Goal:** Maximize the length of this subsequence.

## 💡 Intuition
- **Pick/Not-Pick:** For every element, we have two choices:
  1. **Not Pick:** Move to the next index. The previous element in the subsequence remains the same.
  2. **Pick:** We can only pick the current element if it is strictly greater than the previously picked element (or if it's the first element being picked). If we pick it, its index becomes the new `prev_index`.
- **Recursive Relation:** `f(index, prev_index) = max(not_pick, pick)`.

## 🔁 Approach

### 1. Recursion
- **Explanation:** Use two pointers: `index` (current element) and `prev_index` (last picked element).
- **Code (C++):**
```cpp
int solve(int index, int prev_index, int arr[], int n) {
    if (index == n) return 0;
    
    // Not Pick
    int len = 0 + solve(index + 1, prev_index, arr, n);
    
    // Pick
    if (prev_index == -1 || arr[index] > arr[prev_index]) {
        len = max(len, 1 + solve(index + 1, index, arr, n));
    }
    
    return len;
}
```

### 2. Memoization
- **Explanation:** `prev_index` ranges from `-1` to `n-1`. To store this in a DP table, we apply a **coordinate shift** of +1 to `prev_index`. So `prev_index` `-1` is stored at index `0`.
- **Code (C++):**
```cpp
int solve(int index, int prev_index, int arr[], int n, vector<vector<int>>& dp) {
    if (index == n) return 0;
    
    if (dp[index][prev_index + 1] != -1) return dp[index][prev_index + 1];
    
    // Not Pick
    int len = 0 + solve(index + 1, prev_index, arr, n, dp);
    
    // Pick
    if (prev_index == -1 || arr[index] > arr[prev_index]) {
        len = max(len, 1 + solve(index + 1, index, arr, n, dp));
    }
    
    return dp[index][prev_index + 1] = len;
}
```

### 3. Tabulation (Basic)
- **Explanation:** Fill the table bottom-up.
- **Code (C++):**
```cpp
int longestIncreasingSubsequence(int arr[], int n) {
    vector<vector<int>> dp(n + 1, vector<int>(n + 1, 0));
    
    for (int i = n - 1; i >= 0; i--) {
        for (int prev = i - 1; prev >= -1; prev--) {
            // Not Pick
            int len = 0 + dp[i + 1][prev + 1];
            
            // Pick
            if (prev == -1 || arr[i] > arr[prev]) {
                len = max(len, 1 + dp[i + 1][i + 1]);
            }
            dp[i][prev + 1] = len;
        }
    }
    return dp[0][0];
}
```

## 🧠 DP State Definition
- `dp[i][prev_index + 1]` is the length of the longest increasing subsequence starting from index `i`, where the last element picked was at `prev_index`.

## ⏱️ Complexity
- **Time Complexity:** $O(N^2)$
- **Space Complexity:**
  - $O(N^2)$ for Memoization/Tabulation.
  - $O(N)$ for Space Optimization (using two rows).

## 📌 Pattern
- **LIS Pattern:** One of the most common DP patterns.

## ⚠️ Common Mistakes
- **Coordinate Shift:** Forgetting to shift `prev_index` by +1 when accessing the DP table leads to out-of-bounds errors.
- **Strictly Increasing:** The condition is `arr[index] > arr[prev_index]`. If the problem asks for non-decreasing, use `>=`.

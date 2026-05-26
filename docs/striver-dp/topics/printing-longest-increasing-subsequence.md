# Printing Longest Increasing Subsequence

## 🧩 Problem Summary
- **Problem:** Beyond finding the length of the Longest Increasing Subsequence (LIS), the goal is to actually reconstruct and print the elements of one such subsequence.
- **Goal:** Return the actual elements of the LIS in the correct order.

## 💡 Intuition
- To print the LIS, we need a way to backtrack. 
- The standard $O(N^2)$ tabulation approach (where `dp[i]` is the LIS ending at index `i`) is better suited for this than the "pick/not-pick" 2D DP table.
- We maintain a `hash` array to store the index of the previous element that contributed to the current LIS length.

## 🔁 Approach

### 1. Tabulation Algorithm ($O(N^2)$)
- **Explanation:** 
  1. Initialize a `dp` array of size $N$ with 1s (each element is an LIS of length 1).
  2. For each index `i`, iterate through all previous indices `j` (from `0` to `i-1`).
  3. If `arr[j] < arr[i]` and `1 + dp[j] > dp[i]`, update `dp[i] = 1 + dp[j]`.
- **Code (C++):**
```cpp
int longestIncreasingSubsequence(int arr[], int n) {
    vector<int> dp(n, 1);
    int maxi = 1;
    for (int i = 0; i < n; i++) {
        for (int prev = 0; prev < i; prev++) {
            if (arr[prev] < arr[i]) {
                dp[i] = max(dp[i], 1 + dp[prev]);
            }
        }
        maxi = max(maxi, dp[i]);
    }
    return maxi;
}
```

### 2. Printing the LIS (Backtracking)
- **Explanation:** 
  1. Use a `hash` array to store the index of the predecessor of each element in its LIS.
  2. Keep track of the `lastIndex` where the maximum LIS length was found.
  3. Backtrack from `lastIndex` using the `hash` array until you reach an element that is its own predecessor.
- **Code (C++):**
```cpp
void printLIS(int arr[], int n) {
    vector<int> dp(n, 1);
    vector<int> hash(n);
    int maxi = 1;
    int lastIndex = 0;
    
    for (int i = 0; i < n; i++) {
        hash[i] = i; // Initializing hash with its own index
        for (int prev = 0; prev < i; prev++) {
            if (arr[prev] < arr[i] && 1 + dp[prev] > dp[i]) {
                dp[i] = 1 + dp[prev];
                hash[i] = prev;
            }
        }
        if (dp[i] > maxi) {
            maxi = dp[i];
            lastIndex = i;
        }
    }
    
    vector<int> lis;
    lis.push_back(arr[lastIndex]);
    while (hash[lastIndex] != lastIndex) {
        lastIndex = hash[lastIndex];
        lis.push_back(arr[lastIndex]);
    }
    reverse(lis.begin(), lis.end());
    
    for (auto it : lis) cout << it << " ";
}
```

## 🧠 DP State Definition
- `dp[i]` is the length of the longest increasing subsequence that ends specifically at index `i`.
- `hash[i]` stores the index of the previous element in the LIS that ends at index `i`.

## ⏱️ Complexity
- **Time Complexity:** $O(N^2)$ to fill the `dp` and `hash` arrays, plus $O(N)$ for backtracking. Total $O(N^2)$.
- **Space Complexity:** $O(N)$ to store `dp`, `hash`, and the resulting `lis` vector.

## 📌 Pattern
- **LIS Reconstruction:** Using an auxiliary array (`hash`) to store state transitions for backtracking.

## ⚠️ Common Mistakes
- **Initial Values:** Forgetting that `dp[i]` should start at 1 and `hash[i]` should start at `i`.
- **Max Length Index:** Forgetting that the LIS might not end at the very last element of the input array. You must find the global maximum in the `dp` array.
- **Strictly Increasing:** The condition `arr[prev] < arr[i]` ensures strictly increasing.

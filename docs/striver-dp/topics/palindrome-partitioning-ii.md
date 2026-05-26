# Palindrome Partitioning II (Front Partition)

## 🧩 Problem Summary
- **Problem:** Given a string `s`, partition `s` such that every substring of the partition is a palindrome. Return the **minimum** cuts needed for a palindrome partitioning of `s`.
- **Example:** `s = "aab"`
  - `["a", "a", "b"]` (2 cuts)
  - `["aa", "b"]` (1 cut)
  - Output: 1.

## 💡 Intuition
- **Front Partitioning:** Unlike the standard MCM where we partition in the middle, here we pick a prefix, check if it's a palindrome, and if it is, we recursively solve for the remaining string.
- **Recursive Step:** For a string starting at index `i`, we try every possible cut at index `j` (from `i` to `n-1`). If `s[i...j]` is a palindrome, we make 1 cut and solve for the rest: `1 + f(j + 1)`.
- **Optimization:** To avoid redundant calculations, we use memoization.

## 🔁 Approach

### 1. Recursion with Memoization
- **Explanation:** `f(i)` returns the minimum number of substrings (not cuts) starting from index `i`. At the end, we subtract 1 from the result to get the number of cuts.
- **Code (C++):**
```cpp
bool isPalindrome(int i, int j, string& s) {
    while (i < j) {
        if (s[i++] != s[j--]) return false;
    }
    return true;
}

int solve(int i, int n, string& s, vector<int>& dp) {
    if (i == n) return 0;
    if (dp[i] != -1) return dp[i];

    int minCost = 1e9;
    for (int j = i; j < n; j++) {
        if (isPalindrome(i, j, s)) {
            int cost = 1 + solve(j + 1, n, s, dp);
            minCost = min(minCost, cost);
        }
    }
    return dp[i] = minCost;
}

int minCut(string s) {
    int n = s.size();
    vector<int> dp(n, -1);
    return solve(0, n, s, dp) - 1;
}
```

### 2. Tabulation
- **Explanation:** Build the DP array from `n-1` down to `0`.
- **Code (C++):**
```cpp
int minCut(string s) {
    int n = s.size();
    vector<int> dp(n + 1, 0);
    
    for (int i = n - 1; i >= 0; i--) {
        int minCost = 1e9;
        for (int j = i; j < n; j++) {
            if (isPalindrome(i, j, s)) {
                int cost = 1 + dp[j + 1];
                minCost = min(minCost, cost);
            }
        }
        dp[i] = minCost;
    }
    return dp[0] - 1;
}
```

## 🧠 DP State Definition
- `dp[i]` is the minimum number of palindromic substrings we can get from the suffix of the string starting at index `i`.

## ⏱️ Complexity
- **Time Complexity:** $O(N^2)$ states, and for each state, we run a loop of $O(N)$. Plus `isPalindrome` check is $O(N)$. Total $O(N^3)$.
  - *Note:* `isPalindrome` can be precomputed in $O(N^2)$ to make the total time complexity $O(N^2)$.
- **Space Complexity:** $O(N)$ for the DP table.

## 📌 Pattern
- **Front Partition:** Fixing the left part (if it satisfies a condition) and recursing on the right.

## ⚠️ Common Mistakes
- **Cuts vs. Substrings:** The recurrence `1 + f(j+1)` counts the number of palindromic *segments*. If you have 3 segments, you made 2 cuts. Don't forget the `-1` at the end.
- **Base Case:** When `i == n`, it means we've successfully partitioned the whole string. Return `0` because no more segments are needed.

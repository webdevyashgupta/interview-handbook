# Longest Common Substring

## 🧩 Problem Summary
- **Problem:** Given two strings `S1` and `S2`, find the length of their Longest Common Substring. A substring must be a **consecutive** portion of the string.
- **Input:** String `S1`, String `S2`.
- **Output:** Length of the longest common substring.

## 💡 Intuition
- In LCS (Subsequence), when characters don't match, we carry forward the maximum length found so far by skipping characters.
- In **Substring**, since it must be consecutive, if characters at `S1[i-1]` and `S2[j-1]` don't match, the common substring ending at these indices is **0**.
- **Matching Case:** If `S1[i-1] == S2[j-1]`, we add `1` to the length of the common substring ending at `S1[i-2]` and `S2[j-2]`.

## 🔁 Approach

### 1. Tabulation
- **Logic:**
  - `if S1[i-1] == S2[j-1]: dp[i][j] = 1 + dp[i-1][j-1]`
  - `else: dp[i][j] = 0`
- The answer is the **maximum value** present anywhere in the `dp` table.
- **Code (Python):**
```python
def longest_common_substring(s1, s2):
    n, m = len(s1), len(s2)
    dp = [[0 for _ in range(m + 1)] for _ in range(n + 1)]
    ans = 0
    
    for i in range(1, n + 1):
        for j in range(1, m + 1):
            if s1[i-1] == s2[j-1]:
                dp[i][j] = 1 + dp[i-1][j-1]
                ans = max(ans, dp[i][j])
            else:
                dp[i][j] = 0
    return ans
```

### 2. Space Optimization
- Just like LCS, `dp[i]` only depends on `dp[i-1]`.
- **Code (Python):**
```python
def longest_common_substring(s1, s2):
    n, m = len(s1), len(s2)
    prev = [0] * (m + 1)
    ans = 0
    
    for i in range(1, n + 1):
        cur = [0] * (m + 1)
        for j in range(1, m + 1):
            if s1[i-1] == s2[j-1]:
                cur[j] = 1 + prev[j-1]
                ans = max(ans, cur[j])
            else:
                cur[j] = 0
        prev = cur
    return ans
```

## 🧠 DP State Definition
- `dp[i][j]` is the length of the longest common substring **ending at** `S1[i-1]` and `S2[j-1]`.

## ⏱️ Complexity
- **Time Complexity:** $O(N \times M)$
- **Space Complexity:** $O(M)$ with space optimization.

## 📌 Pattern
- **DP on Strings (Consecutive):** Reset the DP value to `0` on a mismatch to break the continuity.

## ⚠️ Common Mistakes
- **Returning `dp[n][m]`:** Unlike LCS, the answer isn't necessarily at the last cell. It's the maximum value in the entire table.
- **Skipping characters:** Do not take `max(dp[i-1][j], dp[i][j-1])` on mismatch; that is for subsequences.

# Print Longest Common Subsequence

## 🧩 Problem Summary
- **Problem:** Given two strings `S1` and `S2`, find and print the actual Longest Common Subsequence (LCS).
- **Input:** String `S1`, String `S2`.
- **Output:** The LCS string itself.

## 💡 Intuition
- To print the LCS, we first need to compute the DP table using the tabulation approach for the LCS length.
- Once the table is filled, we backtrack from the last cell `dp[n][m]` to the first cell `dp[0][0]`.
- **Backtracking Rules:**
  1. If characters match (`S1[i-1] == S2[j-1]`): This character is part of the LCS. Add it to our result and move diagonally to `dp[i-1][j-1]`.
  2. If they don't match: Move to the neighbor with the larger value (either `dp[i-1][j]` or `dp[i][j-1]`). This ensures we follow the path that contributed to the maximum LCS length.

## 🔁 Approach

### Backtracking from Tabulation Table
- **Code (Python):**
```python
def print_lcs(s1, s2):
    n, m = len(s1), len(s2)
    dp = [[0 for _ in range(m + 1)] for _ in range(n + 1)]

    # Fill the DP table (Standard LCS Tabulation)
    for i in range(1, n + 1):
        for j in range(1, m + 1):
            if s1[i-1] == s2[j-1]:
                dp[i][j] = 1 + dp[i-1][j-1]
            else:
                dp[i][j] = max(dp[i-1][j], dp[i][j-1])

    # Backtracking to find the string
    length = dp[n][m]
    lcs_str = [""] * length
    index = length - 1
    i, j = n, m

    while i > 0 and j > 0:
        if s1[i-1] == s2[j-1]:
            lcs_str[index] = s1[i-1]
            index -= 1
            i -= 1
            j -= 1
        elif dp[i-1][j] > dp[i][j-1]:
            i -= 1
        else:
            j -= 1
            
    return "".join(lcs_str)
```

## 🧠 DP State Definition
- `dp[i][j]` is the length of the LCS of `S1[0...i-1]` and `S2[0...j-1]`.

## ⏱️ Complexity
- **Time Complexity:** $O(N \times M)$ to fill the table + $O(N + M)$ to backtrack.
- **Space Complexity:** $O(N \times M)$ to store the DP table (required for backtracking).

## 📌 Pattern
- **DP Backtracking:** Using a filled DP table to reconstruct the optimal solution.

## ⚠️ Common Mistakes
- **String Construction:** Since we backtrack from the end, either append characters and reverse the string at the end, or fill a fixed-size array from right to left.
- **Equality in Max:** If `dp[i-1][j] == dp[i][j-1]`, moving in either direction is fine, but it might lead to different (equally valid) LCS strings.

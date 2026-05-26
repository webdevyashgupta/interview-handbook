# Shortest Common Supersequence

## 🧩 Problem Summary
- **Problem**: Given two strings `s1` and `s2`, find the shortest string that has both `s1` and `s2` as subsequences.
- **Input**: Two strings `s1` and `s2`.
- **Output**: The shortest common supersequence string.

## 💡 Intuition
- A "supersequence" contains all characters of both strings in their original relative order.
- The simplest supersequence is just `s1 + s2`, but it's not the shortest.
- To make it shortest, we should include the characters that are common to both strings (the Longest Common Subsequence) only once.
- **Formula for Length**: `len(s1) + len(s2) - len(LCS(s1, s2))`.
- To reconstruct the string, we use the DP table from LCS and backtrack.

## 🔁 Approach

### 1. Tabulation (LCS Based)
We first fill the standard LCS table.
```python
def get_lcs_table(s1, s2):
    n, m = len(s1), len(s2)
    dp = [[0] * (m + 1) for _ in range(n + 1)]
    for i in range(1, n + 1):
        for j in range(1, m + 1):
            if s1[i-1] == s2[j-1]:
                dp[i][j] = 1 + dp[i-1][j-1]
            else:
                dp[i][j] = max(dp[i-1][j], dp[i][j-1])
    return dp
```

### 2. Reconstructing the String
Backtrack from `dp[n][m]` to `dp[0][0]`.
```python
def shortestCommonSupersequence(s1, s2):
    n, m = len(s1), len(s2)
    dp = get_lcs_table(s1, s2)
    
    i, j = n, m
    ans = ""
    while i > 0 and j > 0:
        if s1[i-1] == s2[j-1]:
            ans += s1[i-1]
            i -= 1
            j -= 1
        elif dp[i-1][j] > dp[i][j-1]:
            ans += s1[i-1]
            i -= 1
        else:
            ans += s2[j-1]
            j -= 1
            
    # Add remaining characters
    while i > 0:
        ans += s1[i-1]
        i -= 1
    while j > 0:
        ans += s2[j-1]
        j -= 1
        
    return ans[::-1]
```

## 🧠 DP State Definition
- `dp[i][j]`: Length of the Longest Common Subsequence between `s1[0...i-1]` and `s2[0...j-1]`.

## 🔄 Recurrence Relation
- If `s1[i-1] == s2[j-1]`: `dp[i][j] = 1 + dp[i-1][j-1]`
- Else: `dp[i][j] = max(dp[i-1][j], dp[i][j-1])`

## ⏱️ Complexity
- **Time**: $O(N \times M)$ to build the table and $O(N+M)$ to backtrack.
- **Space**: $O(N \times M)$ for the DP table.

## 📌 Pattern
- DP on Strings (LCS Variation).

## ⚠️ Common Mistakes
- Forgetting to add the remaining characters after the `while i > 0 and j > 0` loop finishes.
- Incorrectly choosing which character to add when `s1[i-1] != s2[j-1]`. We should move in the direction of the maximum LCS value.

## 🔗 Related Problems
- Longest Common Subsequence
- Printing Longest Common Subsequence
- Shortest Common Supersequence Length

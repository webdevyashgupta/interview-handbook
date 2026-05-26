# Longest Common Subsequence (DP on Strings)

## 🧩 Problem Summary
- **Problem:** Given two strings `S1` and `S2`, find the length of their Longest Common Subsequence (LCS). A subsequence is a sequence that appears in the same relative order, but not necessarily contiguous.
- **Input:** String `S1`, String `S2`.
- **Output:** Length of the LCS.

## 💡 Intuition
- **Comparison:** Compare characters of both strings from the end (indices `i` and `j`).
  1. **Match:** If `S1[i] == S2[j]`, then this character is part of the LCS. Add `1` to the result and move both pointers back: `1 + f(i-1, j-1)`.
  2. **No Match:** If `S1[i] != S2[j]`, the characters don't match. We have two choices:
     - Shrink `S1`: `f(i-1, j)`
     - Shrink `S2`: `f(i, j-1)`
     - Take the maximum of both: `max(f(i-1, j), f(i, j-1))`.
- **Base Case:** If either index becomes negative (`i < 0` or `j < 0`), return `0` (no more characters to compare).

## 🔁 Approach

### 1. Recursion with Memoization
- **Code (Python):**
```python
def solve(i, j, s1, s2, dp):
    if i < 0 or j < 0:
        return 0
    
    if dp[i][j] != -1:
        return dp[i][j]
    
    if s1[i] == s2[j]:
        dp[i][j] = 1 + solve(i - 1, j - 1, s1, s2, dp)
    else:
        dp[i][j] = max(solve(i - 1, j, s1, s2, dp), solve(i, j - 1, s1, s2, dp))
        
    return dp[i][j]
```

### 2. Tabulation (with Index Shifting)
- **Explanation:** To handle the negative base cases easily in tabulation, shift indices to the right by 1. 
  - `dp[i][j]` will represent LCS of `S1[0...i-1]` and `S2[0...j-1]`.
  - `i=0` or `j=0` now represents the empty string (base case).
- **Code (Python):**
```python
def lcs(s1, s2):
    n, m = len(s1), len(s2)
    dp = [[0 for _ in range(m + 1)] for _ in range(n + 1)]
    
    # Base cases (i=0 or j=0) are already 0
    
    for i in range(1, n + 1):
        for j in range(1, m + 1):
            if s1[i-1] == s2[j-1]:
                dp[i][j] = 1 + dp[i-1][j-1]
            else:
                dp[i][j] = max(dp[i-1][j], dp[i][j-1])
                
    return dp[n][m]
```

### 3. Space Optimization (Two Rows)
- **Explanation:** Each row `i` only depends on the current row and the previous row `i-1`.
- **Code (Python):**
```python
def lcs(s1, s2):
    n, m = len(s1), len(s2)
    prev = [0] * (m + 1)
    
    for i in range(1, n + 1):
        cur = [0] * (m + 1)
        for j in range(1, m + 1):
            if s1[i-1] == s2[j-1]:
                cur[j] = 1 + prev[j-1]
            else:
                cur[j] = max(prev[j], cur[j-1])
        prev = cur
        
    return prev[m]
```

## 🧠 DP State Definition
- `dp[i][j]` is the length of the LCS of `S1` up to index `i` and `S2` up to index `j`.

## ⏱️ Complexity
- **Time Complexity:** $O(N \times M)$
- **Space Complexity:** $O(M)$ with space optimization.

## 📌 Pattern
- **DP on Strings:** Compare characters and branch into match/no-match cases.

## ⚠️ Common Mistakes
- **Index Shifting:** When using shifted indices in tabulation, remember to compare `S1[i-1]` and `S2[j-1]` instead of `S1[i]` and `S2[j]`.
- **Subsequence vs Substring:** Subsequence does not need to be contiguous; substring does.

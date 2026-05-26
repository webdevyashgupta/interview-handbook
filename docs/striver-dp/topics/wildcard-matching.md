# Wildcard Matching

## 🧩 Problem Summary
Given an input string `s2` (text) and a pattern `s1` (wildcard), implement wildcard pattern matching with support for `'?'` and `'*'`.

- `'?'` matches any single character.
- `'*'` matches any sequence of characters (including the empty sequence).
- The matching should cover the entire input string (not partial).

### Input/Output
- **Input**: `s1 = "ab*cd"`, `s2 = "abdefcd"`
- **Output**: `true` (since `*` matches `"def"`)

### Constraints
- `s1` and `s2` contain lowercase English letters.
- `s1` can contain `'?'` and `'*'`.

## 💡 Intuition
The problem is a variation of string matching. The challenge lies in the `'*'` character, which can match zero or more characters. This creates multiple branching paths:
1. Treat `'*'` as an empty string (skip it).
2. Treat `'*'` as matching one character and stay at the same pattern index to potentially match more.

Brute force (recursion) explores all these paths. Overlapping subproblems arise when different paths lead to the same state `(i, j)`, where `i` is the index in the pattern and `j` is the index in the text.

## 🔁 Approach

### 1. Recursion
Define `f(i, j)` as whether `s1[0...i]` matches `s2[0...j]`.
- If `s1[i] == s2[j]` or `s1[i] == '?'`, move to `f(i-1, j-1)`.
- If `s1[i] == '*'`, two choices:
    - Match empty: `f(i-1, j)`
    - Match one or more: `f(i, j-1)`
- Else: `false`.

### 2. Memoization (Top-Down DP)
Use a 2D array `dp[n][m]` to store results of `f(i, j)` to avoid redundant calculations.

### 3. Tabulation (Bottom-Up DP)
Convert the recursion to a 2D table. Handle base cases for `i=0` and `j=0` carefully. Note that `i` and `j` are 1-indexed in tabulation to handle empty string cases.
- `dp[0][0] = true` (both empty match)
- `dp[0][j] = false` (pattern empty, text not)
- `dp[i][0] = true` only if all characters in `s1[0...i-1]` are `'*'`.

### 4. Space Optimization
Since `dp[i][j]` only depends on the current row `i` and the previous row `i-1`, we can use two 1D arrays (`prev` and `curr`) of size `m+1`.

## 🧠 DP State Definition
`dp[i][j]` represents whether the first `i` characters of the pattern match the first `j` characters of the text.

## 🔄 Recurrence Relation
```cpp
if (s1[i-1] == s2[j-1] || s1[i-1] == '?')
    dp[i][j] = dp[i-1][j-1];
else if (s1[i-1] == '*')
    dp[i][j] = dp[i-1][j] || dp[i][j-1];
else
    dp[i][j] = false;
```

## ⏱️ Complexity (Time & Space)
- **Time**: $O(N \times M)$ where $N$ and $M$ are lengths of the strings.
- **Space**: $O(N \times M)$ for Tabulation, $O(M)$ for Space Optimization.

## 📌 Pattern
DP on Strings (Matching Pattern)

## ⚠️ Common Mistakes
- **Base Case for `*`**: A pattern like `***` matches an empty string. The base case must check if all remaining pattern characters are `*`.
- **String Indexing**: Be careful with 1-based indexing in DP vs 0-based in strings.

## 🔗 Related Problems
- Regular Expression Matching
- Longest Common Subsequence
- Edit Distance

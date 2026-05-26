# Distinct Subsequences

## 🧩 Problem Summary
- **Problem**: Given two strings `s1` and `s2`, count the number of distinct subsequences of `s1` which equal `s2`.
- **Input**: Strings `s1` and `s2`.
- **Output**: An integer representing the count of occurrences.

## 💡 Intuition
- This is a string matching problem. We want to find how many ways we can "pick" characters from `s1` to form `s2`.
- At each step, if the characters match (`s1[i] == s2[j]`), we have two choices:
    1.  **Match them**: Move both pointers (`i-1`, `j-1`).
    2.  **Don't match**: Skip the character in `s1` and look for a match later (`i-1`, `j`).
- If they don't match, we only have one choice: skip the character in `s1` (`i-1`, `j`).

## 🔁 Approach

### 1. Recursion + Memoization
```python
def solve(i, j, s1, s2, dp):
    if j < 0: return 1 # s2 is exhausted, found a match
    if i < 0: return 0 # s1 is exhausted, no match possible
    
    if dp[i][j] != -1: return dp[i][j]
    
    if s1[i] == s2[j]:
        dp[i][j] = solve(i-1, j-1, s1, s2, dp) + solve(i-1, j, s1, s2, dp)
    else:
        dp[i][j] = solve(i-1, j, s1, s2, dp)
    return dp[i][j]
```

### 2. Tabulation
```python
def numDistinct(s1, s2):
    n, m = len(s1), len(s2)
    dp = [[0] * (m + 1) for _ in range(n + 1)]
    
    for i in range(n + 1): dp[i][0] = 1 # Empty s2 matches everything once
    
    for i in range(1, n + 1):
        for j in range(1, m + 1):
            if s1[i-1] == s2[j-1]:
                dp[i][j] = dp[i-1][j-1] + dp[i-1][j]
            else:
                dp[i][j] = dp[i-1][j]
    return dp[n][m]
```

### 3. Space Optimization (1D Array)
Since `dp[i][j]` only depends on the previous row's values (`dp[i-1]`), we can optimize to $O(M)$ space.
```python
def numDistinct(s1, s2):
    n, m = len(s1), len(s2)
    prev = [0] * (m + 1)
    prev[0] = 1
    
    for i in range(1, n + 1):
        for j in range(m, 0, -1): # Iterate backwards to use values from current 'prev'
            if s1[i-1] == s2[j-1]:
                prev[j] = prev[j-1] + prev[j]
            # else: prev[j] stays prev[j]
    return prev[m]
```

## 🧠 DP State Definition
- `dp[i][j]`: Number of distinct subsequences of `s2[0...j-1]` in `s1[0...i-1]`.

## 🔄 Recurrence Relation
- If `s1[i-1] == s2[j-1]`: `dp[i][j] = dp[i-1][j-1] + dp[i-1][j]`
- Else: `dp[i][j] = dp[i-1][j]`

## ⏱️ Complexity
- **Time**: $O(N \times M)$
- **Space**: $O(M)$ (Space Optimized)

## 📌 Pattern
- DP on Strings (String Matching).

## ⚠️ Common Mistakes
- Incorrect base cases: `j < 0` should return 1, and `i < 0` should return 0.
- Forgetting the "don't match" case when `s1[i] == s2[j]`. You can still choose to use a different occurrence of that character later in `s1`.

## 🔗 Related Problems
- Longest Common Subsequence
- Edit Distance

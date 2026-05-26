# Edit Distance

## 🧩 Problem Summary
- **Problem**: Given two strings `s1` and `s2`, find the minimum number of operations required to convert `s1` to `s2`.
- **Operations allowed**:
    1.  **Insert** a character.
    2.  **Delete** a character.
    3.  **Replace** a character.
- **Input**: Strings `s1` and `s2`.
- **Output**: Minimum edit distance (integer).

## 💡 Intuition
- This is the classic string matching problem. We compare characters from the end.
- If characters match (`s1[i] == s2[j]`), 0 cost, move both pointers (`i-1`, `j-1`).
- If they don't match, we try all three operations:
    1.  **Insert**: Hypothetically insert `s2[j]` at the end of `s1[i]`. Now the last characters match, so we move only `j-1`. Cost: `1 + f(i, j-1)`.
    2.  **Delete**: Remove `s1[i]`. Now we need to match `s1[i-1]` with `s2[j]`. Cost: `1 + f(i-1, j)`.
    3.  **Replace**: Change `s1[i]` to `s2[j]`. Now they match, move both pointers. Cost: `1 + f(i-1, j-1)`.
- We take the minimum of these three.

## 🔁 Approach

### 1. Recursion + Memoization
```python
def solve(i, j, s1, s2, dp):
    if i < 0: return j + 1 # s1 empty, must insert all of s2
    if j < 0: return i + 1 # s2 empty, must delete all of s1
    
    if dp[i][j] != -1: return dp[i][j]
    
    if s1[i] == s2[j]:
        dp[i][j] = solve(i-1, j-1, s1, s2, dp)
    else:
        insert = 1 + solve(i, j-1, s1, s2, dp)
        delete = 1 + solve(i-1, j, s1, s2, dp)
        replace = 1 + solve(i-1, j-1, s1, s2, dp)
        dp[i][j] = min(insert, delete, replace)
    return dp[i][j]
```

### 2. Tabulation (Bottom-Up)
```python
def minDistance(s1, s2):
    n, m = len(s1), len(s2)
    dp = [[0] * (m + 1) for _ in range(n + 1)]
    
    for i in range(n + 1): dp[i][0] = i
    for j in range(m + 1): dp[0][j] = j
    
    for i in range(1, n + 1):
        for j in range(1, m + 1):
            if s1[i-1] == s2[j-1]:
                dp[i][j] = dp[i-1][j-1]
            else:
                dp[i][j] = 1 + min(dp[i][j-1], dp[i-1][j], dp[i-1][j-1])
    return dp[n][m]
```

### 3. Space Optimization (1D Array)
```python
def minDistance(s1, s2):
    n, m = len(s1), len(s2)
    prev = [j for j in range(m + 1)]
    curr = [0] * (m + 1)
    
    for i in range(1, n + 1):
        curr[0] = i
        for j in range(1, m + 1):
            if s1[i-1] == s2[j-1]:
                curr[j] = prev[j-1]
            else:
                curr[j] = 1 + min(curr[j-1], prev[j], prev[j-1])
        prev = list(curr)
    return prev[m]
```

## 🧠 DP State Definition
- `dp[i][j]`: Minimum operations to convert `s1[0...i-1]` to `s2[0...j-1]`.

## 🔄 Recurrence Relation
- If `s1[i-1] == s2[j-1]`: `f(i, j) = f(i-1, j-1)`
- Else: `f(i, j) = 1 + min(f(i, j-1), f(i-1, j), f(i-1, j-1))`

## ⏱️ Complexity
- **Time**: $O(N \times M)$
- **Space**: $O(M)$ (Space Optimized)

## 📌 Pattern
- DP on Strings (String Matching).

## ⚠️ Common Mistakes
- Base cases: If one string is exhausted, the cost is the remaining length of the other string.
- Mixing up the operations: `(i, j-1)` is Insert, `(i-1, j)` is Delete, `(i-1, j-1)` is Replace.

## 🔗 Related Problems
- Longest Common Subsequence
- Delete Operation for Two Strings
- Wildcard Matching

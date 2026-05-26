# Grid Unique Paths

## 🧩 Problem Summary
- **Problem**: You are given an $M \times N$ grid. You start at the top-left corner $(0, 0)$ and want to reach the bottom-right corner $(M-1, N-1)$. You can only move either **Down** or **Right** at any point in time.
- **Input**: Two integers $M$ and $N$.
- **Output**: The total number of unique paths to reach the destination.
- **Constraints**: $M, N \ge 1$.

## 💡 Intuition
- At any cell $(i, j)$, there are two ways to reach it:
    1. From the cell above: $(i-1, j)$
    2. From the cell to the left: $(i, j-1)$
- The total number of ways to reach $(i, j)$ is the sum of ways to reach $(i-1, j)$ and $(i, j-1)$.
- **Base Case**: There is only 1 way to reach the starting cell $(0, 0)$.

## 🔁 Approach

### 1. Recursion
- **Explanation**: Start from $(M-1, N-1)$ and recursively move up and left until $(0, 0)$ is reached.
- **Code (Python)**:
```python
def count_paths(i, j):
    if i == 0 and j == 0:
        return 1
    if i < 0 or j < 0:
        return 0
    
    up = count_paths(i - 1, j)
    left = count_paths(i, j - 1)
    
    return up + left
```

### 2. Memoization (Top-Down DP)
- **Explanation**: Store the results of subproblems in a 2D array `dp[M][N]` to avoid redundant calculations.
- **Code (Python)**:
```python
def count_paths_memo(m, n):
    dp = [[-1] * n for _ in range(m)]

    def solve(i, j):
        if i == 0 and j == 0:
            return 1
        if i < 0 or j < 0:
            return 0
        if dp[i][j] != -1:
            return dp[i][j]
        
        dp[i][j] = solve(i - 1, j) + solve(i, j - 1)
        return dp[i][j]

    return solve(m - 1, n - 1)
```

### 3. Tabulation (Bottom-Up DP)
- **Explanation**: Fill the DP table iteratively starting from `dp[0][0]`.
- **Code (Python)**:
```python
def count_paths_tab(m, n):
    dp = [[0] * n for _ in range(m)]
    
    for i in range(m):
        for j in range(n):
            if i == 0 and j == 0:
                dp[i][j] = 1
            else:
                up = dp[i-1][j] if i > 0 else 0
                left = dp[i][j-1] if j > 0 else 0
                dp[i][j] = up + left
                
    return dp[m-1][n-1]
```

### 4. Space Optimization
- **Explanation**: To calculate the current row's values, we only need the values from the previous row and the value from the left cell in the current row.
- **Code (Python)**:
```python
def count_paths_opt(m, n):
    prev = [0] * n
    for i in range(m):
        curr = [0] * n
        for j in range(n):
            if i == 0 and j == 0:
                curr[j] = 1
            else:
                up = prev[j]
                left = curr[j-1] if j > 0 else 0
                curr[j] = up + left
        prev = curr
    return prev[n-1]
```

## 🧠 DP State Definition
- `dp[i][j]` = Total number of unique paths from $(0, 0)$ to $(i, j)$.

## 🔄 Recurrence Relation
- $f(i, j) = f(i-1, j) + f(i, j-1)$
- Base Case: $f(0, 0) = 1$

## ⏱️ Complexity
- **Time**: $O(M \times N)$
- **Space**: $O(N)$ (Space Optimized) or $O(M \times N)$ (Tabulation/Memoization)

## 📌 Pattern
- **2D DP**: DP on Grids (Path Counting).

## ⚠️ Common Mistakes
- Not handling out-of-bounds cases (`i < 0` or `j < 0`) in recursion.
- Forgetting the base case `dp[0][0] = 1`.

## 🔗 Related Problems
- [Unique Paths II (with obstacles)](https://leetcode.com/problems/unique-paths-ii/)
- [Minimum Path Sum](https://leetcode.com/problems/minimum-path-sum/)
- [Cherry Pickup](https://leetcode.com/problems/cherry-pickup/)

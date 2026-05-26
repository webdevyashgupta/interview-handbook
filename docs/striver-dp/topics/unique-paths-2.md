# Unique Paths II (Grid with Obstacles)

## 🧩 Problem Summary
- **Problem**: You are given an $N \times M$ grid with some obstacles. You start at the top-left corner $(0, 0)$ and want to reach the bottom-right corner $(N-1, M-1)$. You can only move **Down** or **Right**. If a cell has an obstacle, you cannot pass through it.
- **Input**: An $N \times M$ matrix `maze` where `maze[i][j] = -1` (or 1) represents an obstacle and `0` represents an empty path.
- **Output**: Total number of unique paths to the destination.
- **Constraints**: The answer should be modulo $10^9 + 7$.

## 💡 Intuition
- This is a direct extension of the **Grid Unique Paths** problem.
- The logic remains the same: the number of ways to reach $(i, j)$ is the sum of ways to reach $(i-1, j)$ and $(i, j-1)$.
- **Key Difference**: If a cell $(i, j)$ is an obstacle, the number of ways to reach it is **0**. This effectively stops any paths from passing through that cell.

## 🔁 Approach

### 1. Recursion
- **Explanation**: Similar to Unique Paths I, but with an additional base case for obstacles.
- **Code (Python)**:
```python
def count_paths(i, j, maze):
    if i >= 0 and j >= 0 and maze[i][j] == -1:
        return 0
    if i == 0 and j == 0:
        return 1
    if i < 0 or j < 0:
        return 0
    
    up = count_paths(i - 1, j, maze)
    left = count_paths(i, j - 1, maze)
    
    return up + left
```

### 2. Memoization (Top-Down DP)
- **Explanation**: Cache the results of each cell to optimize the recursion.
- **Code (Python)**:
```python
def solve_memo(m, n, maze):
    dp = [[-1] * n for _ in range(m)]

    def solve(i, j):
        if i >= 0 and j >= 0 and maze[i][j] == -1:
            return 0
        if i == 0 and j == 0:
            return 1
        if i < 0 or j < 0:
            return 0
        if dp[i][j] != -1:
            return dp[i][j]
        
        dp[i][j] = (solve(i - 1, j) + solve(i, j - 1)) % (10**9 + 7)
        return dp[i][j]

    return solve(m - 1, n - 1)
```

### 3. Tabulation (Bottom-Up DP)
- **Explanation**: Iteratively fill the DP table while checking for obstacles at each cell.
- **Code (Python)**:
```python
def solve_tab(m, n, maze):
    dp = [[0] * n for _ in range(m)]
    MOD = 10**9 + 7
    
    for i in range(m):
        for j in range(n):
            if maze[i][j] == -1:
                dp[i][j] = 0
            elif i == 0 and j == 0:
                dp[i][j] = 1
            else:
                up = dp[i-1][j] if i > 0 else 0
                left = dp[i][j-1] if j > 0 else 0
                dp[i][j] = (up + left) % MOD
                
    return dp[m-1][n-1]
```

### 4. Space Optimization
- **Explanation**: Only the previous row is needed.
- **Code (Python)**:
```python
def solve_opt(m, n, maze):
    prev = [0] * n
    MOD = 10**9 + 7
    for i in range(m):
        curr = [0] * n
        for j in range(n):
            if maze[i][j] == -1:
                curr[j] = 0
            elif i == 0 and j == 0:
                curr[j] = 1
            else:
                up = prev[j]
                left = curr[j-1] if j > 0 else 0
                curr[j] = (up + left) % MOD
        prev = curr
    return prev[n-1]
```

## 🧠 DP State Definition
- `dp[i][j]` = Total number of unique paths from $(0, 0)$ to $(i, j)$ avoiding obstacles.

## 🔄 Recurrence Relation
- If `maze[i][j] == -1`: $f(i, j) = 0$
- Else: $f(i, j) = f(i-1, j) + f(i, j-1)$
- Base Case: $f(0, 0) = 1$ (if not an obstacle)

## ⏱️ Complexity
- **Time**: $O(N \times M)$
- **Space**: $O(M)$ (Space Optimized) or $O(N \times M)$ (Tabulation/Memoization)

## 📌 Pattern
- **2D DP**: DP on Grids with Obstacles.

## ⚠️ Common Mistakes
- Not checking if the start cell $(0, 0)$ or end cell $(N-1, M-1)$ is an obstacle.
- Forgetting to apply modulo at each step.

## 🔗 Related Problems
- [Grid Unique Paths](https://leetcode.com/problems/unique-paths/)
- [Minimum Path Sum](https://leetcode.com/problems/minimum-path-sum/)

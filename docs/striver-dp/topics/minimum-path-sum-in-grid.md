# Minimum Path Sum in Grid

## 🧩 Problem Summary
- **Problem**: You are given an $N \times M$ grid filled with non-negative numbers. Find a path from the top-left $(0, 0)$ to the bottom-right $(N-1, M-1)$ which minimizes the sum of all numbers along its path.
- **Input**: An $N \times M$ matrix `grid`.
- **Output**: The minimum path sum.
- **Constraints**: You can only move either **Down** or **Right** at any point in time.

## 💡 Intuition
- **Greedy Fails**: Picking the smallest adjacent cell at each step doesn't guarantee the overall minimum sum. A small value now might lead to a very large value later in the path.
- **Example**:
    ```
    1 10 1
    1  1 1
    ```
    Starting at (0,0), greedy would pick 1 (down) then 1, 1 (total 3). But if (0,0) was:
    ```
    1 2 100
    10 1 1
    ```
    Greedy picks 1 -> 2 -> 100 (total 103), while 1 -> 10 -> 1 -> 1 (total 13) is much better.
- **DP Approach**: Since we want the minimum sum to reach $(i, j)$, it must be the value of the current cell plus the minimum of the sums to reach $(i-1, j)$ (Top) or $(i, j-1)$ (Left).

## 🔁 Approach

### 1. Recursion
- **Explanation**: Recursively find the minimum path sum from the top or left neighbor.
- **Code (Python)**:
```python
def min_path_sum(i, j, grid):
    if i == 0 and j == 0:
        return grid[0][0]
    if i < 0 or j < 0:
        return float('inf') # Return infinity for out of bounds
    
    up = grid[i][j] + min_path_sum(i - 1, j, grid)
    left = grid[i][j] + min_path_sum(i, j - 1, grid)
    
    return min(up, left)
```

### 2. Memoization (Top-Down DP)
- **Explanation**: Store the results in a 2D `dp` table.
- **Code (Python)**:
```python
def solve_memo(m, n, grid):
    dp = [[-1] * n for _ in range(m)]

    def solve(i, j):
        if i == 0 and j == 0:
            return grid[0][0]
        if i < 0 or j < 0:
            return float('inf')
        if dp[i][j] != -1:
            return dp[i][j]
        
        up = grid[i][j] + solve(i - 1, j)
        left = grid[i][j] + solve(i, j - 1)
        
        dp[i][j] = min(up, left)
        return dp[i][j]

    return solve(m - 1, n - 1)
```

### 3. Tabulation (Bottom-Up DP)
- **Explanation**: Fill the table row by row or column by column.
- **Code (Python)**:
```python
def solve_tab(m, n, grid):
    dp = [[0] * n for _ in range(m)]
    
    for i in range(m):
        for j in range(n):
            if i == 0 and j == 0:
                dp[i][j] = grid[0][0]
            else:
                up = dp[i-1][j] if i > 0 else float('inf')
                left = dp[i][j-1] if j > 0 else float('inf')
                dp[i][j] = grid[i][j] + min(up, left)
                
    return dp[m-1][n-1]
```

### 4. Space Optimization
- **Explanation**: Only the previous row is needed.
- **Code (Python)**:
```python
def solve_opt(m, n, grid):
    prev = [0] * n
    for i in range(m):
        curr = [0] * n
        for j in range(n):
            if i == 0 and j == 0:
                curr[j] = grid[0][0]
            else:
                up = prev[j] if i > 0 else float('inf')
                left = curr[j-1] if j > 0 else float('inf')
                curr[j] = grid[i][j] + min(up, left)
        prev = curr
    return prev[n-1]
```

## 🧠 DP State Definition
- `dp[i][j]` = Minimum path sum from $(0, 0)$ to $(i, j)$.

## 🔄 Recurrence Relation
- $f(i, j) = \text{grid}[i][j] + \min(f(i-1, j), f(i, j-1))$
- Base Case: $f(0, 0) = \text{grid}[0][0]$

## ⏱️ Complexity
- **Time**: $O(N \times M)$
- **Space**: $O(M)$ (Space Optimized) or $O(N \times M)$ (Tabulation/Memoization)

## 📌 Pattern
- **2D DP**: DP on Grids (Path Sum).

## ⚠️ Common Mistakes
- Returning `0` for out-of-bounds cases in recursion. For minimization, you must return a very large value (Infinity) so that the path is not chosen.
- Not initializing `dp[0][0]` correctly.

## 🔗 Related Problems
- [Unique Paths](https://leetcode.com/problems/unique-paths/)
- [Unique Paths II](https://leetcode.com/problems/unique-paths-ii/)
- [Triangle](https://leetcode.com/problems/triangle/)

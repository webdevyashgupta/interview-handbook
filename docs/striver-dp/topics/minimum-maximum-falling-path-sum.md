# Minimum/Maximum Falling Path Sum (Variable Starting and Ending Points)

## 🧩 Problem Summary
- **Problem:** Given an $N \times M$ matrix, find the maximum path sum from any cell in the first row to any cell in the last row.
- **Movement:** From a cell $(i, j)$, you can move to $(i+1, j)$ (directly below), $(i+1, j-1)$ (diagonally left), or $(i+1, j+1)$ (diagonally right).
- **Input:** A 2D matrix of integers.
- **Output:** The maximum path sum (integer).
- **Constraints:** Both starting and ending points are variable.

## 💡 Intuition
- **Why Greedy Fails:** Choosing the maximum available move at any point might lead you away from a much larger value in a different part of the grid.
- **Thought Process:**
  - Since we can start from any cell in the first row and end at any cell in the last row, we need to consider all possible starting points.
  - Alternatively, we can think: "What is the max path sum to reach cell $(i, j)$?"
  - This value depends on the cells in the row above it: $(i-1, j)$, $(i-1, j-1)$, and $(i-1, j+1)$.

## 🔁 Approach

### 1. Recursion
- **Explanation:** Define `f(i, j)` as the maximum path sum to reach cell $(i, j)$ from the first row. We call `f(n-1, j)` for every column `j` in the last row and take the maximum.
- **Code (Python):**
```python
def solve(i, j, m, matrix):
    # Boundary Check
    if j < 0 or j >= m:
        return -float('inf')
    # Base Case: First Row
    if i == 0:
        return matrix[0][j]
    
    up = matrix[i][j] + solve(i - 1, j, m, matrix)
    left_diag = matrix[i][j] + solve(i - 1, j - 1, m, matrix)
    right_diag = matrix[i][j] + solve(i - 1, j + 1, m, matrix)
    
    return max(up, left_diag, right_diag)

def getMaxPathSum(matrix):
    n = len(matrix)
    m = len(matrix[0])
    maxi = -float('inf')
    for j in range(m):
        maxi = max(maxi, solve(n - 1, j, m, matrix))
    return maxi
```

### 2. Memoization (Top-Down DP)
- **Explanation:** Use a 2D `dp` table to store results of `f(i, j)` to avoid overlapping subproblems.
- **Code (Python):**
```python
def solve(i, j, m, matrix, dp):
    if j < 0 or j >= m:
        return -float('inf')
    if i == 0:
        return matrix[0][j]
    
    if dp[i][j] != -1:
        return dp[i][j]
    
    up = matrix[i][j] + solve(i - 1, j, m, matrix, dp)
    left_diag = matrix[i][j] + solve(i - 1, j - 1, m, matrix, dp)
    right_diag = matrix[i][j] + solve(i - 1, j + 1, m, matrix, dp)
    
    dp[i][j] = max(up, left_diag, right_diag)
    return dp[i][j]
```

### 3. Tabulation (Bottom-Up DP)
- **Explanation:** Fill the table from the first row downwards.
- **Code (Python):**
```python
def getMaxPathSum(matrix):
    n = len(matrix)
    m = len(matrix[0])
    dp = [[0 for _ in range(m)] for _ in range(n)]
    
    # Base case: first row
    for j in range(m):
        dp[0][j] = matrix[0][j]
        
    for i in range(1, n):
        for j in range(m):
            up = matrix[i][j] + dp[i-1][j]
            
            left_diag = matrix[i][j]
            if j - 1 >= 0: left_diag += dp[i-1][j-1]
            else: left_diag += -float('inf')
            
            right_diag = matrix[i][j]
            if j + 1 < m: right_diag += dp[i-1][j+1]
            else: right_diag += -float('inf')
            
            dp[i][j] = max(up, left_diag, right_diag)
            
    return max(dp[n-1])
```

### 4. Space Optimization
- **Explanation:** The current row only depends on the previous row. Use two arrays: `prev` and `cur`.
- **Code (Python):**
```python
def getMaxPathSum(matrix):
    n = len(matrix)
    m = len(matrix[0])
    prev = matrix[0][:]
    
    for i in range(1, n):
        cur = [0] * m
        for j in range(m):
            up = matrix[i][j] + prev[j]
            
            left_diag = matrix[i][j] + (prev[j-1] if j-1 >= 0 else -float('inf'))
            right_diag = matrix[i][j] + (prev[j+1] if j+1 < m else -float('inf'))
            
            cur[j] = max(up, left_diag, right_diag)
        prev = cur
        
    return max(prev)
```

## 🧠 DP State Definition
- `dp[i][j]` represents the maximum path sum to reach cell `(i, j)` starting from any cell in the first row.

## 🔄 Recurrence Relation
- `f(i, j) = matrix[i][j] + max(f(i-1, j), f(i-1, j-1), f(i-1, j+1))`
- **Base Case:** `f(0, j) = matrix[0][j]`
- **Out of Bounds:** If $j < 0$ or $j \ge M$, return $-\infty$.

## ⏱️ Complexity
- **Time Complexity:** $O(N \times M)$ as we visit each cell once and perform $O(1)$ operations.
- **Space Complexity:**
  - $O(N \times M)$ for Tabulation/Memoization.
  - $O(M)$ for Space Optimization.

## 📌 Pattern
- **DP on Grids:** Variable starting point, variable ending point.

## ⚠️ Common Mistakes
- **Incorrect Out-of-Bound Return:** Returning 0 instead of $-\infty$ for out-of-bounds in a maximum path sum problem can lead to incorrect results if the matrix contains negative numbers.
- **Missing Columns:** Forgetting to iterate through all possible columns in the last row to find the overall maximum.

## 🔗 Related Problems
- [Triangle](https://leetcode.com/problems/triangle/)
- [Minimum Path Sum](https://leetcode.com/problems/minimum-path-sum/)
- [Cherry Pickup II](https://leetcode.com/problems/cherry-pickup-ii/)

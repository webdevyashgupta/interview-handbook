# Triangle (Fixed Starting Point and Variable Ending Point)

## 🧩 Problem Summary
- **Problem:** Given a triangular array, return the minimum path sum from the top to the bottom row.
- **Movement:** From index `j` in row `i`, you can move to index `j` or `j + 1` in row `i + 1`.
- **Input:** A 2D list `triangle` where the $i$-th row has $i+1$ elements.
- **Output:** The minimum path sum (integer).
- **Constraints:** Fixed starting point at `(0, 0)`, variable ending point anywhere in the last row.

## 💡 Intuition
- **Why Greedy Fails:** Choosing the immediate minimum at each step might lead to a path with very large values later on. We need to consider all possible paths.
- **Thought Process:** 
  - We start at a single point `(0, 0)` and want to reach any point in the last row.
  - Since the ending point is variable, starting the recursion from the top `(0, 0)` down to the bottom is more intuitive than starting from the bottom up.
  - At any cell `(i, j)`, we have two choices: go down to `(i+1, j)` or go diagonally to `(i+1, j+1)`.

## 🔁 Approach

### 1. Recursion
- **Explanation:** Start from `(0, 0)` and recursively calculate the min path sum for the two possible movements.
- **Code (Python):**
```python
def solve(i, j, n, triangle):
    if i == n - 1:
        return triangle[i][j]
    
    down = triangle[i][j] + solve(i + 1, j, n, triangle)
    diagonal = triangle[i][j] + solve(i + 1, j + 1, n, triangle)
    
    return min(down, diagonal)

def minimumPathSum(triangle, n):
    return solve(0, 0, n, triangle)
```

### 2. Memoization (Top-Down DP)
- **Explanation:** Store the results of subproblems in a `dp` table to avoid redundant calculations.
- **Code (Python):**
```python
def solve(i, j, n, triangle, dp):
    if i == n - 1:
        return triangle[i][j]
    
    if dp[i][j] != -1:
        return dp[i][j]
    
    down = triangle[i][j] + solve(i + 1, j, n, triangle, dp)
    diagonal = triangle[i][j] + solve(i + 1, j + 1, n, triangle, dp)
    
    dp[i][j] = min(down, diagonal)
    return dp[i][j]

def minimumPathSum(triangle, n):
    dp = [[-1 for _ in range(n)] for _ in range(n)]
    return solve(0, 0, n, triangle, dp)
```

### 3. Tabulation (Bottom-Up DP)
- **Explanation:** Start from the bottom row and fill the `dp` table upwards to the top.
- **Code (Python):**
```python
def minimumPathSum(triangle, n):
    dp = [[0 for _ in range(n)] for _ in range(n)]
    
    # Base case: last row
    for j in range(n):
        dp[n-1][j] = triangle[n-1][j]
    
    # Fill upwards
    for i in range(n - 2, -1, -1):
        for j in range(i + 1):
            down = triangle[i][j] + dp[i+1][j]
            diagonal = triangle[i][j] + dp[i+1][j+1]
            dp[i][j] = min(down, diagonal)
            
    return dp[0][0]
```

### 4. Space Optimization
- **Explanation:** Each row only depends on the row directly below it. We can use a single array to store the "next" row's results.
- **Code (Python):**
```python
def minimumPathSum(triangle, n):
    # Start with the last row
    front = [triangle[n-1][j] for j in range(n)]
    
    for i in range(n - 2, -1, -1):
        cur = [0 for _ in range(i + 1)]
        for j in range(i + 1):
            down = triangle[i][j] + front[j]
            diagonal = triangle[i][j] + front[j+1]
            cur[j] = min(down, diagonal)
        front = cur
        
    return front[0]
```

## 🧠 DP State Definition
- `dp[i][j]` represents the minimum path sum to reach the bottom row starting from cell `(i, j)`.

## 🔄 Recurrence Relation
- `f(i, j) = triangle[i][j] + min(f(i+1, j), f(i+1, j+1))`
- **Base Case:** `f(n-1, j) = triangle[n-1][j]` for all $j \in [0, n-1]$.

## ⏱️ Complexity
- **Time Complexity:** $O(N \times N)$ where $N$ is the number of rows. We visit each cell once.
- **Space Complexity:** 
  - $O(N \times N)$ for Memoization/Tabulation.
  - $O(N)$ for Space Optimization (storing one row).

## 📌 Pattern
- **DP on Grids:** Fixed starting point, variable ending point (multi-target).

## ⚠️ Common Mistakes
- **Recursion Direction:** Trying to start from the last row in recursion is harder because there are multiple starting points. Starting from the top `(0, 0)` is simpler.
- **Boundary Checks:** Unlike rectangular grids, the number of columns changes per row in a triangle. Ensure loops for `j` only go up to `i`.

## 🔗 Related Problems
- [Minimum Path Sum in Grid](https://leetcode.com/problems/minimum-path-sum/)
- [Unique Paths II](https://leetcode.com/problems/unique-paths-ii/)
- [Falling Path Sum](https://leetcode.com/problems/minimum-falling-path-sum/)

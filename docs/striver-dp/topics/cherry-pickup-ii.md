# Cherry Pickup II (3D DP on Grids)

## 🧩 Problem Summary
- **Problem:** Alice and Bob start at the top row of an $R \times C$ grid at $(0, 0)$ and $(0, C-1)$ respectively. They want to collect the maximum total chocolates while moving down to the last row.
- **Movement:** In each step, both move to the next row ($i+1$) and can choose one of three columns: $j-1, j, j+1$.
- **Rules:**
  - If they land in the same cell, chocolates are collected only once.
  - If they land in different cells, both collection amounts are added.
- **Input:** 2D grid of chocolates.
- **Output:** Maximum chocolates collected.

## 💡 Intuition
- **Simultaneous Movement:** Alice and Bob move row-by-row together. This means their row index $i$ is always the same.
- **State Representation:** To capture the positions of both friends, we need the row $i$ and their respective columns $j_1$ and $j_2$. This leads to a 3D state $(i, j_1, j_2)$.
- **Why Greedy Fails:** Alice might pick a large number of chocolates now, but in doing so, she might force Bob into a path with very few chocolates, or vice versa. We must explore all 9 possible combinations of their moves $(3 \times 3)$.

## 🔁 Approach

### 1. Recursion
- **Explanation:** Define `f(i, j1, j2)` as the max chocolates from row `i` to the bottom.
- **Code (Python):**
```python
def solve(i, j1, j2, r, c, grid):
    # Boundary Check
    if j1 < 0 or j1 >= c or j2 < 0 or j2 >= c:
        return -float('inf')
    
    # Base Case: Last Row
    if i == r - 1:
        if j1 == j2:
            return grid[i][j1]
        else:
            return grid[i][j1] + grid[i][j2]
            
    maxi = -float('inf')
    # Explore all 9 paths
    for dj1 in [-1, 0, 1]:
        for dj2 in [-1, 0, 1]:
            value = 0
            if j1 == j2:
                value = grid[i][j1]
            else:
                value = grid[i][j1] + grid[i][j2]
            
            value += solve(i + 1, j1 + dj1, j2 + dj2, r, c, grid)
            maxi = max(maxi, value)
            
    return maxi
```

### 2. Memoization (Top-Down DP)
- **Explanation:** Store results in a 3D table `dp[r][c][c]`.
- **Code (Python):**
```python
def solve(i, j1, j2, r, c, grid, dp):
    if j1 < 0 or j1 >= c or j2 < 0 or j2 >= c:
        return -float('inf')
    if i == r - 1:
        return grid[i][j1] + (grid[i][j2] if j1 != j2 else 0)
    
    if dp[i][j1][j2] != -1:
        return dp[i][j1][j2]
        
    maxi = -float('inf')
    for dj1 in [-1, 0, 1]:
        for dj2 in [-1, 0, 1]:
            ans = grid[i][j1] + (grid[i][j2] if j1 != j2 else 0)
            ans += solve(i + 1, j1 + dj1, j2 + dj2, r, c, grid, dp)
            maxi = max(maxi, ans)
            
    dp[i][j1][j2] = maxi
    return dp[i][j1][j2]
```

### 3. Tabulation (Bottom-Up DP)
- **Explanation:** Fill the 3D table from the bottom row up to the top row.
- **Code (Python):**
```python
def cherryPickup(grid):
    r = len(grid)
    c = len(grid[0])
    dp = [[[0 for _ in range(c)] for _ in range(c)] for _ in range(r)]
    
    # Base case: last row
    for j1 in range(c):
        for j2 in range(c):
            if j1 == j2:
                dp[r-1][j1][j2] = grid[r-1][j1]
            else:
                dp[r-1][j1][j2] = grid[r-1][j1] + grid[r-1][j2]
                
    # Fill from r-2 to 0
    for i in range(r - 2, -1, -1):
        for j1 in range(c):
            for j2 in range(c):
                maxi = -float('inf')
                for dj1 in [-1, 0, 1]:
                    for dj2 in [-1, 0, 1]:
                        ans = grid[i][j1] + (grid[i][j2] if j1 != j2 else 0)
                        if 0 <= j1+dj1 < c and 0 <= j2+dj2 < c:
                            ans += dp[i+1][j1+dj1][j2+dj2]
                        else:
                            ans += -float('inf')
                        maxi = max(maxi, ans)
                dp[i][j1][j2] = maxi
                
    return dp[0][0][c-1]
```

### 4. Space Optimization
- **Explanation:** The current row $i$ only depends on row $i+1$. We can reduce the 3D DP to two 2D layers: `front[c][c]` and `cur[c][c]`.
- **Code (Python):**
```python
def cherryPickup(grid):
    r, c = len(grid), len(grid[0])
    front = [[0]*c for _ in range(c)]
    
    for j1 in range(c):
        for j2 in range(c):
            front[j1][j2] = grid[r-1][j1] + (grid[r-1][j2] if j1 != j2 else 0)
            
    for i in range(r-2, -1, -1):
        cur = [[0]*c for _ in range(c)]
        for j1 in range(c):
            for j2 in range(c):
                maxi = -float('inf')
                for dj1 in [-1, 0, 1]:
                    for dj2 in [-1, 0, 1]:
                        val = grid[i][j1] + (grid[i][j2] if j1 != j2 else 0)
                        if 0 <= j1+dj1 < c and 0 <= j2+dj2 < c:
                            val += front[j1+dj1][j2+dj2]
                        else:
                            val += -float('inf')
                        maxi = max(maxi, val)
                cur[j1][j2] = maxi
        front = cur
        
    return front[0][c-1]
```

## 🧠 DP State Definition
- `dp[i][j1][j2]` represents the maximum chocolates Alice and Bob can collect starting from row `i`, with Alice at column `j1` and Bob at column `j2`, until the last row.

## 🔄 Recurrence Relation
- $f(i, j1, j2) = \text{chocolates at } (i, j1, j2) + \max(f(i+1, j1 \pm \{0, 1\}, j2 \pm \{0, 1\}))$
- **Base Case:** Row $i = R-1$ returns the sum of chocolates at current columns.

## ⏱️ Complexity
- **Time Complexity:** $O(R \times C \times C \times 9) \approx O(R \times C^2)$.
- **Space Complexity:** 
  - $O(R \times C^2)$ for 3D DP.
  - $O(C^2)$ for Space Optimization.

## 📌 Pattern
- **3D DP on Grids:** Simultaneous movement of two entities.

## ⚠️ Common Mistakes
- **Same Cell Counting:** Forgetting to check `if j1 == j2` and adding the chocolate count twice.
- **Out of Bounds:** Failing to check boundaries for both Alice and Bob in all 9 movement combinations.
- **Return Type:** Returning 0 for out-of-bounds instead of a very large negative number.

## 🔗 Related Problems
- [Cherry Pickup](https://leetcode.com/problems/cherry-pickup/)
- [Minimum Falling Path Sum](https://leetcode.com/problems/minimum-falling-path-sum/)
- [Minimum Path Sum](https://leetcode.com/problems/minimum-path-sum/)

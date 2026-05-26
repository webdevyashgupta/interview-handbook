# Dynamic Programming Templates (Python)

Standard templates to solve DP problems in Python. These templates follow a consistent structure for different optimization levels.

---

## 1. Recursion with Memoization (Top-Down)
This is the most intuitive approach. We use a dictionary or an array to store results of subproblems.

```python
from functools import lru_cache

def solve():
    arr = [1, 2, 3, 4]
    n = len(arr)
    
    # Using lru_cache for automatic memoization
    @lru_cache(None)
    def f(ind):
        # 1. Base Case
        if ind == 0:
            return arr[0]
        if ind < 0:
            return 0
        
        # 2. Recursive calls (Pick / Non-Pick or state transitions)
        pick = arr[ind] + f(ind - 2)
        not_pick = 0 + f(ind - 1)
        
        # 3. Return result
        return max(pick, not_pick)

    return f(n - 1)

# Manual Memoization Template
def solve_manual():
    arr = [1, 2, 3, 4]
    n = len(arr)
    dp = [-1] * n
    
    def f(ind):
        if ind == 0: return arr[0]
        if ind < 0: return 0
        
        if dp[ind] != -1: return dp[ind]
        
        pick = arr[ind] + f(ind - 2)
        not_pick = 0 + f(ind - 1)
        
        dp[ind] = max(pick, not_pick)
        return dp[ind]
        
    return f(n - 1)
```

---

## 2. Tabulation (Bottom-Up)
Iterative approach that fills a DP table. Useful for avoiding recursion depth limits.

```python
def solve_tabulation():
    arr = [1, 2, 3, 4]
    n = len(arr)
    dp = [0] * n
    
    # 1. Initialize Base Case
    dp[0] = arr[0]
    
    # 2. Iterate through states
    for i in range(1, n):
        pick = arr[i]
        if i > 1:
            pick += dp[i - 2]
        
        not_pick = 0 + dp[i - 1]
        
        # 3. Store result in table
        dp[i] = max(pick, not_pick)
        
    return dp[n - 1]
```

---

## 3. Space Optimization (1D DP)
Reduces space from $O(N)$ to $O(1)$ by only keeping track of required previous states.

```python
def solve_space_optimized():
    arr = [1, 2, 3, 4]
    n = len(arr)
    
    prev = arr[0]      # dp[i-1]
    prev2 = 0         # dp[i-2]
    
    for i in range(1, n):
        pick = arr[i] + prev2
        not_pick = 0 + prev
        
        curi = max(pick, not_pick)
        
        # Move pointers forward
        prev2 = prev
        prev = curi
        
    return prev
```

---

## 4. Space Optimization (2D/Grid DP)
Reduces space from $O(N \times M)$ to $O(M)$ by using only the `prev` row.

```python
def solve_grid_space_optimized(matrix):
    n = len(matrix)
    m = len(matrix[0])
    
    # Use a single row to store results of the previous row
    prev = [0] * m
    
    for i in range(n):
        temp = [0] * m
        for j in range(m):
            if i == 0 and j == 0:
                temp[j] = matrix[0][0]
            else:
                up = matrix[i][j]
                if i > 0: up += prev[j]
                else: up += float('inf') # Or suitable boundary value
                
                left = matrix[i][j]
                if j > 0: left += temp[j-1]
                else: left += float('inf')
                
                temp[j] = min(up, left)
        prev = temp
        
    return prev[m-1]
```

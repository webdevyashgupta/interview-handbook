# Frog Jump with K Distance

## 🧩 Problem Summary
- **Problem**: A frog is at the 1st stair and wants to reach the $N$-th stair. Instead of just 1 or 2 steps, the frog can jump up to $K$ steps ($i+1, i+2, \dots, i+K$). The cost of a jump is $|H[i] - H[j]|$.
- **Input**: Array of heights $H$, number of stairs $N$, and maximum jump distance $K$.
- **Output**: Minimum energy used to reach the last stair.

## 💡 Intuition
- This is a generalization of the "Frog Jump" problem.
- Instead of checking only two previous states ($i-1, i-2$), we now check $K$ previous states.
- The thought process remains the same: to reach state $i$, we could have come from any state from $i-1$ to $i-K$.

## 🔁 Approach

### 1. Recursion
- **Explanation**: Iterate through all possible jumps from $1$ to $K$ and find the minimum cost.
- **Code (Python)**:
```python
def solve(i, k, heights):
    if i == 0:
        return 0
    
    min_steps = float('inf')
    for j in range(1, k + 1):
        if i - j >= 0:
            jump = solve(i - j, k, heights) + abs(heights[i] - heights[i-j])
            min_steps = min(min_steps, jump)
    return min_steps
```

### 2. Memoization (Top-Down DP)
- **Explanation**: Cache the results of `solve(i)` in a DP array.
- **Code (Python)**:
```python
def solve_memo(i, k, heights, dp):
    if i == 0:
        return 0
    if dp[i] != -1:
        return dp[i]
    
    min_steps = float('inf')
    for j in range(1, k + 1):
        if i - j >= 0:
            jump = solve_memo(i - j, k, heights, dp) + abs(heights[i] - heights[i-j])
            min_steps = min(min_steps, jump)
    
    dp[i] = min_steps
    return dp[i]
```

### 3. Tabulation (Bottom-Up DP)
- **Explanation**: Use a nested loop where the outer loop iterates through stairs $1$ to $N-1$ and the inner loop iterates through $K$ possible jumps.
- **Code (Python)**:
```python
def solve_tab(n, k, heights):
    dp = [0] * n
    dp[0] = 0
    for i in range(1, n):
        min_steps = float('inf')
        for j in range(1, k + 1):
            if i - j >= 0:
                jump = dp[i-j] + abs(heights[i] - heights[i-j])
                min_steps = min(min_steps, jump)
        dp[i] = min_steps
    return dp[n-1]
```

### 4. Space Optimization
- **Explanation**: We can optimize space from $O(N)$ to $O(K)$ by keeping only the last $K$ values. However, if $K=N$ in the worst case, the space complexity remains $O(N)$. For most interview purposes, the $O(N)$ tabulation is acceptable.

## 🧠 DP State Definition
- `dp[i]` = Minimum energy required to reach the $i$-th stair.

## 🔄 Recurrence Relation
- $f(i) = \min_{1 \le j \le K} \{ f(i-j) + |H[i] - H[i-j]| \}$
- Base Case: $f(0) = 0$

## ⏱️ Complexity
- **Time**: $O(N \times K)$
- **Space**: $O(N)$ (Tabulation/Memoization)

## 📌 Pattern
- **1D DP**: Min/Max Path Cost with $K$ transitions.

## ⚠️ Common Mistakes
- Not checking `i - j >= 0` before accessing the array or calling recursion.
- Using a fixed number of variables (like `prev1`, `prev2`) instead of a loop when $K$ is dynamic.

## 🔗 Related Problems
- [Frog Jump](https://www.geeksforgeeks.org/problems/frog-jump-1643814011/1)
- [Climbing Stairs](https://leetcode.com/problems/climbing-stairs/)

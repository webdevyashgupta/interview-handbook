# Frog Jump

## 🧩 Problem Summary
- **Problem**: A frog is at the 1st stair and wants to reach the $N$-th stair. Each stair has a height $H[i]$. The frog can jump from stair $i$ to $i+1$ or $i+2$. The energy lost in a jump from $i$ to $j$ is $|H[i] - H[j]|$.
- **Input**: Array of heights and $N$.
- **Output**: Minimum total energy to reach the $N$-th stair.
- **Constraints**: $N$ up to $10^5$.

## 💡 Intuition
- The frog wants the path with the minimum energy cost.
- A greedy approach (choosing the smallest jump cost at each step) fails because a slightly more expensive jump now might lead to a much cheaper path overall in the future.
- We must explore all possibilities (recursion) and optimize using DP.

## 🔁 Approach

### 1. Recursion
- **Explanation**: $f(i)$ is the min energy to reach stair $i$ from stair 0.
- **Code (Python)**:
```python
def frog_jump(i, heights):
    if i == 0:
        return 0
    jump_one = frog_jump(i-1, heights) + abs(heights[i] - heights[i-1])
    jump_two = float('inf')
    if i > 1:
        jump_two = frog_jump(i-2, heights) + abs(heights[i] - heights[i-2])
    return min(jump_one, jump_two)
```

### 2. Memoization (Top-Down DP)
- **Explanation**: Store the minimum energy to reach each stair in a `dp` array.
- **Code (Python)**:
```python
def solve(i, heights, dp):
    if i == 0:
        return 0
    if dp[i] != -1:
        return dp[i]
    
    jump_one = solve(i-1, heights, dp) + abs(heights[i] - heights[i-1])
    jump_two = float('inf')
    if i > 1:
        jump_two = solve(i-2, heights, dp) + abs(heights[i] - heights[i-2])
    
    dp[i] = min(jump_one, jump_two)
    return dp[i]
```

### 3. Tabulation (Bottom-Up DP)
- **Explanation**: Iteratively compute the minimum energy for each stair from 0 to $N-1$.
- **Code (Python)**:
```python
def frog_jump_tab(n, heights):
    dp = [0] * n
    dp[0] = 0
    for i in range(1, n):
        fs = dp[i-1] + abs(heights[i] - heights[i-1])
        ss = float('inf')
        if i > 1:
            ss = dp[i-2] + abs(heights[i] - heights[i-2])
        dp[i] = min(fs, ss)
    return dp[n-1]
```

### 4. Space Optimization
- **Explanation**: To calculate `dp[i]`, we only need `dp[i-1]` and `dp[i-2]`.
- **Code (Python)**:
```python
def frog_jump_opt(n, heights):
    prev, prev2 = 0, 0
    for i in range(1, n):
        fs = prev + abs(heights[i] - heights[i-1])
        ss = float('inf')
        if i > 1:
            ss = prev2 + abs(heights[i] - heights[i-2])
        
        curr = min(fs, ss)
        prev2 = prev
        prev = curr
    return prev
```

## 🧠 DP State Definition
- `dp[i]` = Minimum energy required to reach the $i$-th stair from the 0th stair.

## 🔄 Recurrence Relation
- $f(i) = \min($
    $f(i-1) + |H[i] - H[i-1]|,$
    $f(i-2) + |H[i] - H[i-2]|$
  $)$
- Base Case: $f(0) = 0$

## ⏱️ Complexity
- **Time**: $O(N)$
- **Space**: $O(1)$ (Space Optimized)

## 📌 Pattern
- **1D DP**: Min/Max Path Cost.

## ⚠️ Common Mistakes
- Not handling the `i > 1` condition for the second jump, which leads to index out of bounds.
- Incorrectly initializing `jump_two` with 0 instead of infinity when it's not possible.

## 🔗 Related Problems
- [Frog Jump with K Distance](https://www.geeksforgeeks.org/minimal-cost-to-reach-last-stair-with-at-most-k-jumps/)
- [Climbing Stairs](https://leetcode.com/problems/climbing-stairs/)

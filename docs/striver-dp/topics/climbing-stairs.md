# Climbing Stairs

## 🧩 Problem Summary
- **Problem**: You are at the 0th stair and want to reach the $N$-th stair. Each time you can either climb 1 step or 2 steps.
- **Input**: An integer $N$ representing the number of stairs.
- **Output**: The number of distinct ways to reach the $N$-th stair.
- **Constraints**: $N \ge 0$.

## 💡 Intuition
- To reach the $N$-th stair, you must have come from either the $(N-1)$-th stair (by taking a 1-step jump) or the $(N-2)$-th stair (by taking a 2-step jump).
- Total ways to reach $N$ is the sum of ways to reach $(N-1)$ and ways to reach $(N-2)$.
- This problem maps directly to the Fibonacci sequence.

## 🔁 Approach

### 1. Recursion
- **Explanation**: Based on the recurrence $f(n) = f(n-1) + f(n-2)$.
- **Code (Python)**:
```python
def climb_stairs(n):
    if n <= 1:
        return 1
    return climb_stairs(n-1) + climb_stairs(n-2)
```

### 2. Memoization (Top-Down DP)
- **Explanation**: Use a DP array to store results of previously calculated stairs.
- **Code (Python)**:
```python
def climb_stairs_memo(n, dp):
    if n <= 1:
        return 1
    if dp[n] != -1:
        return dp[n]
    dp[n] = climb_stairs_memo(n-1, dp) + climb_stairs_memo(n-2, dp)
    return dp[n]
```

### 3. Tabulation (Bottom-Up DP)
- **Explanation**: Fill an array from 0 to $N$ iteratively.
- **Code (Python)**:
```python
def climb_stairs_tab(n):
    if n <= 1:
        return 1
    dp = [0] * (n + 1)
    dp[0] = 1
    dp[1] = 1
    for i in range(2, n + 1):
        dp[i] = dp[i-1] + dp[i-2]
    return dp[n]
```

### 4. Space Optimization
- **Explanation**: Only track the ways to reach the previous two stairs.
- **Code (Python)**:
```python
def climb_stairs_opt(n):
    if n <= 1:
        return 1
    prev2, prev = 1, 1
    for i in range(2, n + 1):
        curr = prev + prev2
        prev2 = prev
        prev = curr
    return prev
```

## 🧠 DP State Definition
- `dp[i]` = Number of distinct ways to reach the $i$-th stair.

## 🔄 Recurrence Relation
- $f(n) = f(n-1) + f(n-2)$
- Base cases: $f(0) = 1, f(1) = 1$

## ⏱️ Complexity
- **Time**: $O(N)$
- **Space**: $O(1)$ (with space optimization)

## 📌 Pattern
- **1D DP**: Similar to Fibonacci.
- **Count Ways**: The core goal is to find the total number of paths.

## ⚠️ Common Mistakes
- **Base Case**: Thinking $f(0)=0$. There is 1 way to be at the 0th stair (by doing nothing).
- **Large N**: For $N > 45$, integers might overflow in some languages (though Python handles large integers automatically).

## 🔗 Related Problems
- [Frog Jump](https://leetcode.com/problems/frog-jump-with-k-distance/)
- [Min Cost Climbing Stairs](https://leetcode.com/problems/min-cost-climbing-stairs/)

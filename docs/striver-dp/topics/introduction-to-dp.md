# Introduction to Dynamic Programming

## 🧩 Problem Summary
- **Problem**: Calculate the $N$-th Fibonacci number.
- **Input**: An integer $N$.
- **Output**: The $N$-th Fibonacci number where $F(0)=0, F(1)=1$ and $F(n) = F(n-1) + F(n-2)$.
- **Constraints**: $N \ge 0$. Standard recursion fails for large $N$ due to exponential time complexity.

## 💡 Intuition
- **The Core Idea**: "Those who cannot remember the past are condemned to repeat it."
- **Evolution**: Simple recursion recalculates the same subproblems multiple times (e.g., $F(2)$ is calculated many times when finding $F(5)$). This is known as **Overlapping Subproblems**.
- **Solution**: Store the results of subproblems so they are calculated only once.

## 🔁 Approach

### 1. Recursion
- **Explanation**: A direct implementation of the recurrence relation. It has a high cost because it branches out into a tree of redundant calls.
- **Code (Python)**:
```python
def fibonacci(n):
    if n <= 1:
        return n
    return fibonacci(n-1) + fibonacci(n-2)
```

### 2. Memoization (Top-Down DP)
- **Explanation**: Store the result of each function call in an array or hash map. Before computing $F(n)$, check if it's already in the storage.
- **Code (Python)**:
```python
def fibonacci_memo(n, dp):
    if n <= 1:
        return n
    if dp[n] != -1:
        return dp[n]
    dp[n] = fibonacci_memo(n-1, dp) + fibonacci_memo(n-2, dp)
    return dp[n]

n = 5
dp = [-1] * (n + 1)
print(fibonacci_memo(n, dp))
```

### 3. Tabulation (Bottom-Up DP)
- **Explanation**: Use a table (array) to store results starting from the base cases ($0$ and $1$) and iteratively fill the table up to $N$.
- **Code (Python)**:
```python
def fibonacci_tab(n):
    if n <= 1:
        return n
    dp = [-1] * (n + 1)
    dp[0], dp[1] = 0, 1
    for i in range(2, n + 1):
        dp[i] = dp[i-1] + dp[i-2]
    return dp[n]
```

### 4. Space Optimization
- **Explanation**: Since we only need the last two values ($F(i-1)$ and $F(i-2)$) to calculate $F(i)$, we can replace the array with just two variables.
- **Code (Python)**:
```python
def fibonacci_opt(n):
    if n <= 1:
        return n
    prev2, prev = 0, 1
    for i in range(2, n + 1):
        curr = prev + prev2
        prev2 = prev
        prev = curr
    return prev
```

## 🧠 DP State Definition
- `dp[i]` = The $i$-th Fibonacci number.

## 🔄 Recurrence Relation
- $F(n) = F(n-1) + F(n-2)$
- Base cases: $F(0) = 0, F(1) = 1$

## ⏱️ Complexity
- **Time**: $O(N)$ for Memoization, Tabulation, and Space Optimization. $O(2^N)$ for pure Recursion.
- **Space**: 
  - Memoization: $O(N)$ (Array) + $O(N)$ (Recursion Stack)
  - Tabulation: $O(N)$ (Array)
  - Space Optimized: $O(1)$

## 📌 Pattern
- **1D DP**: The state depends on a single parameter (the index $n$).

## ⚠️ Common Mistakes
- Not initializing the DP array correctly (e.g., forgetting the size $N+1$).
- Forgetting the base cases in tabulation.
- Not considering the recursion stack depth in memoization for very large $N$.

## 🔗 Related Problems
- [Climbing Stairs](https://leetcode.com/problems/climbing-stairs/)
- [N-th Tribonacci Number](https://leetcode.com/problems/n-th-tribonacci-number/)

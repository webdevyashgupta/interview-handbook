# Matrix Chain Multiplication

## 🧩 Problem Summary
- **Problem**: Given a sequence of matrices, find the most efficient way to multiply them. The problem is not actually to perform the multiplications, but merely to decide in which order to perform the multiplications.
- **Cost**: Multiplying a matrix of size $A \times B$ with $B \times C$ takes $A \cdot B \cdot C$ operations.
- **Input**: An array `p` where matrix $M_i$ has dimensions $p[i-1] \times p[i]$.
- **Output**: The minimum number of multiplications needed to multiply all matrices.

## 💡 Intuition
1. **Associativity**: Matrix multiplication is associative, meaning $(AB)C = A(BC)$. However, the number of operations can differ significantly.
2. **Partitioning**: To multiply matrices from index $i$ to $j$, we can split them at some index $k$ ($i \le k < j$). We first calculate the cost to multiply $M_i \dots M_k$, then $M_{k+1} \dots M_j$, and finally add the cost to multiply the two resulting matrices.
3. **Overlapping Subproblems**: Calculating the optimal split for $M_i \dots M_j$ requires knowing the optimal splits for smaller ranges, which are recalculated many times in a naive recursive approach.

## 🔁 Approach

### 1. Partition DP (Recursive with Memoization)
- Define a function `f(i, j)` that returns the minimum cost to multiply matrices from index $i$ to $j$.
- Base case: If $i == j$, cost is 0 (only one matrix).
- Recurrence: Try every possible split point $k$ between $i$ and $j$.

### 2. Implementation (Python)
```python
def matrixMultiplication(p):
    n = len(p)
    # matrices are M1, M2, ..., M(n-1)
    # M_i has dimensions p[i-1] x p[i]
    dp = [[-1] * n for _ in range(n)]

    def solve(i, j):
        if i == j:
            return 0
        if dp[i][j] != -1:
            return dp[i][j]
        
        min_cost = float('inf')
        for k in range(i, j):
            # Cost = solve(left) + solve(right) + cost of multiplying results
            cost = solve(i, k) + solve(k + 1, j) + (p[i-1] * p[k] * p[j])
            min_cost = min(min_cost, cost)
            
        dp[i][j] = min_cost
        return min_cost

    return solve(1, n - 1)
```

### 3. Tabulation (Bottom-Up)
- Fill the `dp` table for increasing range lengths (from length 2 up to $n-1$).

```python
def matrixMultiplicationTab(p):
    n = len(p)
    dp = [[0] * n for _ in range(n)]
    
    # len is the length of the chain
    for length in range(2, n): 
        for i in range(1, n - length + 1):
            j = i + length - 1
            dp[i][j] = float('inf')
            for k in range(i, j):
                cost = dp[i][k] + dp[k+1][j] + (p[i-1] * p[k] * p[j])
                dp[i][j] = min(dp[i][j], cost)
                
    return dp[1][n-1]
```

## 🧠 DP State Definition
- `dp[i][j]` = Minimum multiplications needed to multiply matrices from index $i$ to $j$.

## 🔄 Recurrence Relation
- $f(i, j) = \min_{i \le k < j} \{ f(i, k) + f(k+1, j) + p[i-1] \cdot p[k] \cdot p[j] \}$
- Base case: $f(i, i) = 0$

## ⏱️ Complexity
- **Time**: $O(N^3)$ because there are $N^2$ states and each state takes $O(N)$ to compute (looping through $k$).
- **Space**: $O(N^2)$ for the DP table.

## 📌 Pattern
- **Partition DP**: Solving subproblems on intervals $[i, j]$ by splitting at $k$.

## ⚠️ Common Mistakes
- **Index Confusion**: The input array `p` of length $N$ defines $N-1$ matrices. $M_i$ is $p[i-1] \times p[i]$. Ensure loops start from 1.
- **Incorrect Cost Calculation**: Forgetting the $p[i-1] \cdot p[k] \cdot p[j]$ term, which represents the final multiplication of the two combined matrices.

## 🔗 Related Problems
- [Burst Balloons](https://leetcode.com/problems/burst-balloons/)
- [Minimum Cost to Cut a Stick](https://leetcode.com/problems/minimum-cost-to-cut-a-stick/)
- [Boolean Parenthesization](https://practice.geeksforgeeks.org/problems/boolean-parenthesization5610/1)

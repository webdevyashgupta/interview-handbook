# Maximum Sum of Non-Adjacent Elements

## 🧩 Problem Summary
- **Problem**: Given an array of $N$ integers, find the maximum sum of a subsequence such that no two elements in the subsequence are adjacent in the original array.
- **Input**: Array of integers.
- **Output**: Maximum sum.
- **Constraints**: $N \ge 0$, Elements are non-negative.

## 💡 Intuition
- This is the classic **House Robber** problem.
- At each element, you have two choices:
    1. **Pick**: Take the current element and jump to the element at `index - 2` (to avoid adjacency).
    2. **Not Pick**: Skip the current element and move to `index - 1`.
- We want to maximize the total sum of our choices.

## 🔁 Approach

### 1. Recursion
- **Explanation**: Use a recursive function `f(index)` that returns the maximum sum from index 0 to `index`.
- **Code (Python)**:
```python
def solve(i, arr):
    if i == 0:
        return arr[0]
    if i < 0:
        return 0
    
    pick = arr[i] + solve(i - 2, arr)
    not_pick = 0 + solve(i - 1, arr)
    
    return max(pick, not_pick)
```

### 2. Memoization (Top-Down DP)
- **Explanation**: Cache the results of each index to avoid redundant calculations.
- **Code (Python)**:
```python
def solve_memo(i, arr, dp):
    if i == 0:
        return arr[0]
    if i < 0:
        return 0
    if dp[i] != -1:
        return dp[i]
    
    pick = arr[i] + solve_memo(i - 2, arr, dp)
    not_pick = 0 + solve_memo(i - 1, arr, dp)
    
    dp[i] = max(pick, not_pick)
    return dp[i]
```

### 3. Tabulation (Bottom-Up DP)
- **Explanation**: Fill a DP table iteratively from index 0 to $N-1$.
- **Code (Python)**:
```python
def solve_tab(n, arr):
    dp = [0] * n
    dp[0] = arr[0]
    for i in range(1, n):
        pick = arr[i]
        if i > 1:
            pick += dp[i-2]
        not_pick = 0 + dp[i-1]
        dp[i] = max(pick, not_pick)
    return dp[n-1]
```

### 4. Space Optimization
- **Explanation**: Only the previous two values (`dp[i-1]` and `dp[i-2]`) are needed to calculate `dp[i]`.
- **Code (Python)**:
```python
def solve_opt(n, arr):
    prev = arr[0]
    prev2 = 0
    for i in range(1, n):
        pick = arr[i]
        if i > 1:
            pick += prev2
        not_pick = 0 + prev
        
        curr = max(pick, not_pick)
        prev2 = prev
        prev = curr
    return prev
```

## 🧠 DP State Definition
- `dp[i]` = Maximum sum of non-adjacent elements considering the array up to index $i$.

## 🔄 Recurrence Relation
- $f(i) = \max($
    $arr[i] + f(i-2),$
    $0 + f(i-1)$
  $)$
- Base Case: $f(0) = arr[0], f(i < 0) = 0$

## ⏱️ Complexity
- **Time**: $O(N)$
- **Space**: $O(1)$ (Space Optimized)

## 📌 Pattern
- **1D DP**: DP on Subsequences (Pick/Non-Pick).

## ⚠️ Common Mistakes
- Not handling the `i-2` index correctly when `i=1`.
- Forgetting to include the current element `arr[i]` when picking.

## 🔗 Related Problems
- [House Robber](https://leetcode.com/problems/house-robber/)
- [House Robber II](https://leetcode.com/problems/house-robber-ii/)
- [Delete and Earn](https://leetcode.com/problems/delete-and-earn/)

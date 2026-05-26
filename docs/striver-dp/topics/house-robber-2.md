# House Robber II

## 🧩 Problem Summary
- **Problem**: A professional robber plans to rob houses along a circular street. Each house has a certain amount of money. You cannot rob two adjacent houses as they are connected to a security system. Since the street is circular, the first and last houses are also adjacent.
- **Input**: An array of non-negative integers `money`.
- **Output**: The maximum amount of money you can rob tonight without alerting the police.
- **Constraints**: 
    - The houses are arranged in a circle.
    - Adjacent houses (including first and last) cannot be robbed together.

## 💡 Intuition
- This problem is a direct variation of **House Robber I** (Maximum Sum of Non-Adjacent Elements).
- In House Robber I, the houses were in a straight line. Here, the circular constraint means if we pick the first house, we **cannot** pick the last house, and vice versa.
- **The Core Idea**:
    1. The answer will either NOT include the first house.
    2. Or the answer will NOT include the last house.
- We can simply run the House Robber I algorithm twice:
    - Once on the array excluding the first element (`arr[1:]`).
    - Once on the array excluding the last element (`arr[0:-1]`).
- The final result is the maximum of these two results.

## 🔁 Approach

### 1. Recursion
- **Explanation**: Use the standard non-adjacent sum logic but on two different slices of the array.
- **Code (Python)**:
```python
def solve(arr):
    def helper(i, sub_arr):
        if i == 0: return sub_arr[0]
        if i < 0: return 0
        
        pick = sub_arr[i] + helper(i - 2, sub_arr)
        not_pick = 0 + helper(i - 1, sub_arr)
        return max(pick, not_pick)

    n = len(arr)
    if n == 1: return arr[0]
    
    # Case 1: Exclude first, Case 2: Exclude last
    res1 = helper(n - 2, arr[1:])
    res2 = helper(n - 2, arr[:-1])
    return max(res1, res2)
```

### 2. Memoization (Top-Down DP)
- **Explanation**: Cache results for the sub-problems to optimize recursion.
- **Code (Python)**:
```python
def rob(nums):
    def solve_memo(i, arr, dp):
        if i == 0: return arr[0]
        if i < 0: return 0
        if dp[i] != -1: return dp[i]
        
        pick = arr[i] + solve_memo(i - 2, arr, dp)
        not_pick = 0 + solve_memo(i - 1, arr, dp)
        
        dp[i] = max(pick, not_pick)
        return dp[i]

    n = len(nums)
    if n == 1: return nums[0]
    
    arr1 = nums[1:]
    arr2 = nums[:-1]
    
    dp1 = [-1] * (n - 1)
    dp2 = [-1] * (n - 1)
    
    return max(solve_memo(n-2, arr1, dp1), solve_memo(n-2, arr2, dp2))
```

### 3. Tabulation (Bottom-Up DP)
- **Explanation**: Build the DP table for both cases.
- **Code (Python)**:
```python
def rob(nums):
    def solve_tab(arr):
        n = len(arr)
        dp = [0] * n
        dp[0] = arr[0]
        for i in range(1, n):
            pick = arr[i]
            if i > 1: pick += dp[i-2]
            not_pick = dp[i-1]
            dp[i] = max(pick, not_pick)
        return dp[n-1]

    n = len(nums)
    if n == 1: return nums[0]
    return max(solve_tab(nums[1:]), solve_tab(nums[:-1]))
```

### 4. Space Optimization
- **Explanation**: Since House Robber I only needs the last two results, we can reduce space to $O(1)$.
- **Code (Python)**:
```python
def rob(nums):
    def solve_opt(arr):
        prev = arr[0]
        prev2 = 0
        for i in range(1, len(arr)):
            pick = arr[i] + prev2
            not_pick = prev
            curr = max(pick, not_pick)
            prev2 = prev
            prev = curr
        return prev

    n = len(nums)
    if n == 1: return nums[0]
    if n == 0: return 0
    
    # Case 1: Exclude first, Case 2: Exclude last
    res1 = solve_opt(nums[1:])
    res2 = solve_opt(nums[:-1])
    
    return max(res1, res2)
```

## 🧠 DP State Definition
- `dp[i]` = Maximum money robbed considering houses up to index $i$ in a linear arrangement.

## 🔄 Recurrence Relation
- $f(i) = \max(arr[i] + f(i-2), f(i-1))$
- For House Robber II: $\text{Result} = \max(f(arr[1 \dots N-1]), f(arr[0 \dots N-2]))$

## ⏱️ Complexity
- **Time**: $O(N)$ (We traverse the array twice).
- **Space**: $O(1)$ (Space optimized version).

## 📌 Pattern
- **1D DP**: DP on Subsequences (Circular version).

## ⚠️ Common Mistakes
- Not handling the edge case where the array has only 1 element.
- Including both the first and last house in the same sub-problem logic.

## 🔗 Related Problems
- [House Robber I](https://leetcode.com/problems/house-robber/)
- [Maximum Sum of Non-Adjacent Elements](https://www.codingninjas.com/codestudio/problems/maximum-sum-of-non-adjacent-elements_843261)

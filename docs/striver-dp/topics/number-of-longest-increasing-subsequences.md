# Number of Longest Increasing Subsequences

## 🧩 Problem Summary
- **Problem**: Given an integer array `nums`, return the number of longest increasing subsequences.
- **Input**: An array of integers `nums`.
- **Output**: The count of subsequences that have the maximum possible length.

## 💡 Intuition
1. **Extension of LIS**: In the standard LIS problem, we only care about the **maximum length**. Here, we need both the maximum length and **how many ways** we can achieve it.
2. **Two Arrays**:
    - `dp[i]`: Stores the length of the LIS ending at index `i`.
    - `count[i]`: Stores the number of LIS of length `dp[i]` ending at index `i`.
3. **Updating Logic**:
    - When considering a previous element `j` for the current element `i` (where `nums[i] > nums[j]`):
        - If `dp[j] + 1 > dp[i]`: We found a longer LIS. Reset `dp[i] = dp[j] + 1` and inherit the count: `count[i] = count[j]`.
        - If `dp[j] + 1 == dp[i]`: We found another way to reach the same maximum length. Add the counts: `count[i] += count[j]`.

## 🔁 Approach

### 1. Dynamic Programming with Count
- Initialize `dp[i] = 1` and `count[i] = 1` for all indices.
- Perform the nested loop LIS logic.
- After filling the arrays, find the `max_len` in the `dp` array.
- Sum up all `count[i]` where `dp[i] == max_len`.

### 2. Implementation (Python)
```python
def findNumberOfLIS(nums):
    n = len(nums)
    if n <= 1: return n
    
    dp = [1] * n
    count = [1] * n
    
    for i in range(n):
        for j in range(i):
            if nums[i] > nums[j]:
                if dp[j] + 1 > dp[i]:
                    dp[i] = dp[j] + 1
                    count[i] = count[j]
                elif dp[j] + 1 == dp[i]:
                    count[i] += count[j]
                    
    max_len = max(dp)
    total_count = 0
    for i in range(n):
        if dp[i] == max_len:
            total_count += count[i]
            
    return total_count
```

## 🧠 DP State Definition
- `dp[i]` = Length of LIS ending at index `i`.
- `count[i]` = Number of LIS of length `dp[i]` ending at index `i`.

## 🔄 Recurrence Relation
- If $nums[i] > nums[j]$:
    - If $dp[j] + 1 > dp[i]$: $dp[i] = dp[j] + 1$, $count[i] = count[j]$
    - If $dp[j] + 1 == dp[i]$: $count[i] = count[i] + count[j]$

## ⏱️ Complexity
- **Time**: $O(N^2)$ for the nested loops.
- **Space**: $O(N)$ for the `dp` and `count` arrays.

## 📌 Pattern
- **LIS with Counting**: Adding a secondary DP array to track paths/ways.

## ⚠️ Common Mistakes
- **Initialization**: Initializing `count` with 0 instead of 1. Each element is an LIS of length 1 by itself.
- **Summing incorrectly**: Only summing `count[i]` where `dp[i]` equals the global maximum length, not just any length.

## 🔗 Related Problems
- [Longest Increasing Subsequence](https://leetcode.com/problems/longest-increasing-subsequence/)
- [Number of Dice Rolls With Target Sum](https://leetcode.com/problems/number-of-dice-rolls-with-target-sum/)

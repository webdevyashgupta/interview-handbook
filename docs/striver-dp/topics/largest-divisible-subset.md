# Largest Divisible Subset

## 🧩 Problem Summary
- **Problem**: Given a set of **distinct** positive integers, find the largest subset such that every pair $(a, b)$ in the subset satisfies either $a \% b == 0$ or $b \% a == 0$.
- **Input**: An array of distinct positive integers `nums`.
- **Output**: Any one of the largest divisible subsets.
- **Constraints**: 
    - The elements are distinct.
    - $1 \le nums.length \le 1000$.
    - $1 \le nums[i] \le 2 \cdot 10^9$.

## 💡 Intuition
1. **Subset vs. Subsequence**: Since the order doesn't matter for a subset, we can **sort the array** first.
2. **Sorting Advantage**: If we sort the array (e.g., $[1, 4, 7, 8, 16]$), and we are at an element $nums[i]$, any element $nums[j]$ that divides it ($j < i$) will also be divided by anything that divides $nums[j]$.
    - If $8 \% 4 == 0$ and $4 \% 1 == 0$, then $8 \% 1 == 0$ is automatically guaranteed.
3. **LIS Connection**: Once sorted, the problem transforms into finding the **Longest Increasing Subsequence** where the condition is `nums[i] % nums[j] == 0` instead of `nums[i] > nums[j]`.

## 🔁 Approach

### 1. Dynamic Programming (Based on LIS)
- **Sort** the input array.
- Use a `dp` array where `dp[i]` stores the length of the largest divisible subset ending at index `i`.
- Use a `hash` array (or `parent` array) to keep track of the previous index to reconstruct the subset.
- Keep track of the maximum length and the index where it ends.

### 2. Implementation (Python)
```python
def largestDivisibleSubset(nums):
    n = len(nums)
    if n == 0: return []
    
    nums.sort()
    dp = [1] * n
    parent = list(range(n))
    
    max_len = 1
    last_idx = 0
    
    for i in range(n):
        for j in range(i):
            if nums[i] % nums[j] == 0 and dp[i] < dp[j] + 1:
                dp[i] = dp[j] + 1
                parent[i] = j
        
        if dp[i] > max_len:
            max_len = dp[i]
            last_idx = i
            
    # Reconstruct the subset
    result = []
    while parent[last_idx] != last_idx:
        result.append(nums[last_idx])
        last_idx = parent[last_idx]
    result.append(nums[last_idx])
    
    return result[::-1]
```

## 🧠 DP State Definition
- `dp[i]` = Length of the largest divisible subset ending with `nums[i]`.

## 🔄 Recurrence Relation
- $dp[i] = \max(1, 1 + dp[j])$ for all $j < i$ such that $nums[i] \% nums[j] == 0$.

## ⏱️ Complexity
- **Time**: $O(N^2)$ due to the nested loops (similar to LIS). Sorting takes $O(N \log N)$.
- **Space**: $O(N)$ for the `dp` and `parent` arrays.

## 📌 Pattern
- **LIS Pattern**: This is a direct application of the LIS technique with a modified condition and path reconstruction.

## ⚠️ Common Mistakes
- **Forgetting to sort**: The divisibility chain logic only works efficiently if the array is sorted.
- **Returning only the length**: The problem asks for the subset itself, so path reconstruction is required.

## 🔗 Related Problems
- [Longest Increasing Subsequence](https://leetcode.com/problems/longest-increasing-subsequence/)
- [Longest String Chain](https://leetcode.com/problems/longest-string-chain/)

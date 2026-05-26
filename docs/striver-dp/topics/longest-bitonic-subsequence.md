# Longest Bitonic Subsequence

## 🧩 Problem Summary
- **Problem**: Find the length of the longest bitonic subsequence of a given array.
- **Bitonic Subsequence**: A sequence that first increases and then decreases. 
    - Formally: $a_1 < a_2 < \dots < a_k > a_{k+1} > \dots > a_n$.
    - A purely increasing or purely decreasing sequence is also considered bitonic.
- **Input**: An array of integers `nums`.
- **Output**: The length of the longest bitonic subsequence.

## 💡 Intuition
1. **The Peak**: Every bitonic subsequence has a "peak" element. At index $i$, the subsequence is increasing up to $i$ and decreasing after $i$.
2. **Two LIS arrays**:
    - We need the length of the **Longest Increasing Subsequence** ending at index $i$. Let's call this `LIS[i]`.
    - We also need the length of the **Longest Decreasing Subsequence** starting at index $i$. This is equivalent to finding the LIS of the array if we traverse it from right to left. Let's call this `LDS[i]`.
3. **Combination**: The longest bitonic subsequence peaking at index $i$ will have length `LIS[i] + LDS[i] - 1` (we subtract 1 because the element at index $i$ is counted in both).

## 🔁 Approach

### 1. Two-Pass DP
- **Step 1**: Calculate `dp1[i]` (LIS from left to right) for all $i$.
- **Step 2**: Calculate `dp2[i]` (LIS from right to left, which is LDS) for all $i$.
- **Step 3**: Iterate through all indices $i$ and find the maximum value of `dp1[i] + dp2[i] - 1`.

### 2. Implementation (Python)
```python
def longestBitonicSequence(nums):
    n = len(nums)
    if n == 0: return 0
    
    # LIS from left to right
    dp1 = [1] * n
    for i in range(n):
        for j in range(i):
            if nums[i] > nums[j] and dp1[i] < dp1[j] + 1:
                dp1[i] = dp1[j] + 1
                
    # LIS from right to left (LDS)
    dp2 = [1] * n
    for i in range(n - 1, -1, -1):
        for j in range(n - 1, i, -1):
            if nums[i] > nums[j] and dp2[i] < dp2[j] + 1:
                dp2[i] = dp2[j] + 1
                
    max_bitonic = 0
    for i in range(n):
        max_bitonic = max(max_bitonic, dp1[i] + dp2[i] - 1)
        
    return max_bitonic
```

## 🧠 DP State Definition
- `dp1[i]` = Length of LIS ending at index `i`.
- `dp2[i]` = Length of LDS starting at index `i`.

## 🔄 Recurrence Relation
- For `dp1[i]`: $dp1[i] = \max(1, 1 + dp1[j])$ for $j < i, nums[j] < nums[i]$.
- For `dp2[i]`: $dp2[i] = \max(1, 1 + dp2[j])$ for $j > i, nums[j] < nums[i]$.

## ⏱️ Complexity
- **Time**: $O(N^2)$ to compute both `dp1` and `dp2`.
- **Space**: $O(N)$ for the two DP arrays.

## 📌 Pattern
- **Double LIS**: Using LIS logic from both directions to solve a peak-based problem.

## ⚠️ Common Mistakes
- **Subtracting 1**: Forgetting that the peak element is counted in both `dp1` and `dp2`.
- **Strictly Bitonic**: Some variations of the problem require that both the increasing and decreasing parts must have length $> 1$. If so, check `dp1[i] > 1 and dp2[i] > 1` before calculating.

## 🔗 Related Problems
- [Longest Increasing Subsequence](https://leetcode.com/problems/longest-increasing-subsequence/)
- [Mountain Array Problems](https://leetcode.com/problems/longest-mountain-in-array/)

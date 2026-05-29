# Count Subarray Sum Equals K

## Problem Summary
Given an array of integers `nums` and an integer `k`, return the total number of subarrays whose sum equals to `k`. A subarray is a contiguous non-empty sequence of elements within an array.

## Intuition
The problem asks for contiguous segments. While a sliding window might work for positive numbers, the presence of negatives or zeros makes the **Prefix Sum** technique more robust. If the difference between the current prefix sum and a previously seen prefix sum is exactly `k`, then the segment between those two points sums to `k`.

## Approach

### Brute Force
Iterate through all possible subarrays using three nested loops.
1.  Outer loop for starting index `i`.
2.  Inner loop for ending index `j`.
3.  Third loop to calculate sum from `i` to `j`.
- **Time Complexity:** $O(N^3)$
- **Space Complexity:** $O(1)$

### Better
Optimize the brute force by calculating the sum on the fly in the second loop.
1.  Outer loop for starting index `i`.
2.  Inner loop for ending index `j`, adding `nums[j]` to current sum.
- **Time Complexity:** $O(N^2)$
- **Space Complexity:** $O(1)$

### Optimal (Hashing with Prefix Sum)
Use a Hash Map to store the frequency of prefix sums encountered so far.
1.  Maintain a `current_sum` and a `count`.
2.  Map `prefixSumMap` stores `{prefix_sum: frequency}`. Initialize it with `{0: 1}` to handle cases where `current_sum == k`.
3.  For each element in the array:
    - Add it to `current_sum`.
    - Calculate `remove = current_sum - k`.
    - If `remove` exists in the map, add its frequency to `count`.
    - Update the map with the `current_sum`.
- **Time Complexity:** $O(N)$ (or $O(N \log N)$ depending on the map implementation).
- **Space Complexity:** $O(N)$ to store prefix sums.

## Complexity
- **Time Complexity:** $O(N)$
- **Space Complexity:** $O(N)$

## Pattern
Prefix Sum + Hashing.

## Common Mistakes
- **Missing Base Case:** Forgetting to initialize the map with `{0: 1}`.
- **Negative Numbers:** Trying to use a Two-Pointer/Sliding Window approach when the array contains negative numbers (Sliding window typically only works for non-negative integers).

## Related Problems
- Longest Subarray with Sum K
- Subarray Sums Divisible by K
- Continuous Subarray Sum

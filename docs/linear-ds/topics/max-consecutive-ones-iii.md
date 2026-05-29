# Max Consecutive Ones III

## Problem Summary
Given a binary array `nums` and an integer `k`, return the maximum number of consecutive `1`s in the array if you can flip at most `k` `0`s.

## Intuition
The problem can be rephrased as: "Find the longest subarray that contains at most `k` zeros." If we can flip `k` zeros, any subarray with $\le k$ zeros can be turned into a subarray of all ones.

## Approach

### 1. Brute Force
- **Idea**: Generate all possible subarrays and count the number of zeros in each.
- **Optimization**: For each starting index `i`, expand the end index `j`. Keep a count of zeros. Stop expanding when the count of zeros exceeds `k`.
- **Time Complexity**: $O(N^2)$
- **Space Complexity**: $O(1)$

### 2. Better Approach (Sliding Window - Standard)
- **Idea**: Use two pointers, `left` and `right`. Expand `right` and count zeros. If `zeros > k`, shrink the window from the `left` until `zeros <= k`.
- **Steps**:
    1. Initialize `left = 0`, `right = 0`, `zeros = 0`, `max_len = 0`.
    2. Iterate through the array with `right`.
    3. If `nums[right] == 0`, increment `zeros`.
    4. While `zeros > k`:
        - If `nums[left] == 0`, decrement `zeros`.
        - Increment `left`.
    5. Update `max_len = max(max_len, right - left + 1)`.
- **Time Complexity**: $O(2N)$ (each element is visited at most twice).
- **Space Complexity**: $O(1)$

### 3. Optimal Approach (Sliding Window - $O(N)$)
- **Idea**: Instead of shrinking the window with a `while` loop, we can just move `left` forward by one whenever the condition is violated. This keeps the window size from growing until we find a more optimal solution.
- **Steps**:
    1. Initialize `left = 0`, `right = 0`, `zeros = 0`, `max_len = 0`.
    2. Iterate through the array with `right`.
    3. If `nums[right] == 0`, increment `zeros`.
    4. If `zeros > k`: (Note the `if` instead of `while`)
        - If `nums[left] == 0`, decrement `zeros`.
        - Increment `left`.
    5. If `zeros <= k`, update `max_len = max(max_len, right - left + 1)`.
- **Time Complexity**: $O(N)$
- **Space Complexity**: $O(1)$

## Complexity
- **Time Complexity**: $O(N)$ for the optimal sliding window approach.
- **Space Complexity**: $O(1)$ as we only use a few variables.

## Pattern
- **Sliding Window (At most K condition)**: This pattern is common for problems asking for the longest subarray satisfying a condition that is monotonic (adding elements makes it harder to satisfy).

## Common Mistakes
- **Condition for shrinking**: Forgetting to decrement the zero count when moving the `left` pointer.
- **Off-by-one errors**: Incorrectly calculating `right - left + 1`.

## Related Problems
- Longest Substring with At Most K Distinct Characters
- Fruit Into Baskets
- Subarrays with K Different Integers

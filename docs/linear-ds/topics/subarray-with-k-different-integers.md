# Subarrays with K Different Integers

## Problem Summary
Given an integer array `nums` and an integer `k`, return the number of **good subarrays** of `nums`. A subarray is **good** if the number of different integers in that subarray is exactly `k`.

## Intuition
Counting subarrays with *exactly* `k` distinct integers directly using a sliding window is challenging because as we shrink or expand the window, the count of distinct elements doesn't change in a way that easily lets us count all valid subarrays.

However, it is much easier to count subarrays with *at most* `k` distinct integers.
- If a window `[L, R]` has at most `k` distinct integers, then every subarray starting from `L, L+1, ..., R` and ending at `R` also has at most `k` distinct integers.
- The number of such subarrays ending at `R` is `R - L + 1`.

Using this, we can apply the formula:
**`Count(Exactly K) = Count(At Most K) - Count(At Most K - 1)`**

## Approach

### 1. Brute Force
- Iterate through all possible subarrays using two nested loops.
- Use a set or a frequency map to count the number of distinct integers in each subarray.
- Increment the global count if the distinct count equals `k`.
- **Time Complexity:** $O(N^2)$.
- **Space Complexity:** $O(N)$ (for the set/map).

### 2. Optimal (Sliding Window)
- Define a helper function `countSubarraysWithAtMost(nums, k)`:
    - If `k == 0`, return `0`.
    - Use two pointers `L` and `R` to maintain a sliding window.
    - Use a hash map to store the frequency of elements in the current window.
    - Expand `R` and update the frequency of `nums[R]`.
    - While the number of distinct elements (`map.size()`) exceeds `k`:
        - Decrement the frequency of `nums[L]`.
        - If the frequency of `nums[L]` becomes `0`, remove it from the map.
        - Increment `L`.
    - Add `(R - L + 1)` to the result (total subarrays ending at `R` with $\le k$ distinct elements).
- The final result is: `countSubarraysWithAtMost(nums, k) - countSubarraysWithAtMost(nums, k - 1)`.

## Complexity
- **Time Complexity:** $O(N)$. We make two passes over the array, and each pass is $O(N)$ because the pointers `L` and `R` only move forward.
- **Space Complexity:** $O(N)$ in the worst case to store the frequency map of distinct elements.

## Pattern
- **Sliding Window (Helper function for "At Most K"):** This is a powerful pattern for "Exactly K" problems where the property is monotonic (like distinct elements or sums of non-negative integers).

## Common Mistakes
- **Incorrectly counting subarrays:** Not realizing that `R - L + 1` correctly counts all valid subarrays ending at the current pointer `R`.
- **Map handling:** Forgetting to remove an element from the map when its frequency reaches zero (merely setting frequency to 0 doesn't decrease `map.size()`).

## Related Problems
- [Binary Subarrays With Sum](./binary-subarrays-with-sum.md)
- [Count Number of Nice Subarrays](./count-number-of-nice-subarrays.md)
- [Longest Substring with At Most K Distinct Characters](./longest-substring-with-at-most-k-distinct-characters.md)

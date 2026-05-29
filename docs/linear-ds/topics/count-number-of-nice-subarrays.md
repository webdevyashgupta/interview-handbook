# Count Number of Nice Subarrays

## Problem Summary
Given an array of integers `nums` and an integer `k`. A continuous subarray is called **nice** if there are `k` odd numbers in it. Return the number of nice subarrays.

## Intuition
This problem is a direct mapping of the "Binary Subarrays With Sum" problem.
- An odd number can be treated as a `1`.
- An even number can be treated as a `0`.
- The condition "exactly `k` odd numbers" is equivalent to "subarray sum equals `k`" in the transformed binary array.

Just like the binary subarray problem, finding the *exact* count of subarrays with a specific property can be simplified by calculating:
**`Count(Exactly K) = Count(At Most K) - Count(At Most K-1)`**

## Approach

### 1. Brute Force
- Check every possible subarray.
- Count the number of odd integers in each.
- Increment the global count if the odd count equals `k`.
- **Time Complexity:** $O(N^2)$.
- **Space Complexity:** $O(1)$.

### 2. Better (Prefix Sum + Hashing)
- Use a hash map to store the frequency of the "prefix count" of odd numbers.
- As you iterate, maintain a running count of odd numbers encountered.
- If the current count is `currCount`, the number of nice subarrays ending here is the frequency of `currCount - k` in the map.
- **Time Complexity:** $O(N)$.
- **Space Complexity:** $O(N)$.

### 3. Optimal (Sliding Window)
- Convert the problem into "At Most K" odd numbers.
- Helper function `countAtMost(nums, k)`:
    - If `k < 0`, return `0`.
    - Use two pointers `L` and `R`.
    - Maintain `currOddCount` in the window.
    - If `nums[R]` is odd, `currOddCount++`.
    - While `currOddCount > k`, if `nums[L]` is odd, `currOddCount--`, then `L++`.
    - Add `(R - L + 1)` to the result (this represents all subarrays ending at `R` with at most `k` odd numbers).
- Final Result: `countAtMost(nums, k) - countAtMost(nums, k - 1)`.

## Complexity
- **Time Complexity:** $O(N)$. We iterate through the array twice (for `countAtMost(k)` and `countAtMost(k-1)`).
- **Space Complexity:** $O(1)$. No extra space proportional to input size is used.

## Pattern
- **Problem Transformation:** Mapping properties (odd/even) to binary values (1/0).
- **Sliding Window:** Using "At Most K" to solve "Exactly K".

## Common Mistakes
- **Incorrect "At Most" logic:** Forgetting that `R - L + 1` counts all subarrays *ending* at `R` within the valid window.
- **Handling k=0:** Ensure `countAtMost(nums, -1)` returns 0.

## Related Problems
- [Binary Subarrays With Sum](./binary-subarrays-with-sum.md)
- [Subarrays with K Different Integers](./subarray-with-k-different-integers.md)

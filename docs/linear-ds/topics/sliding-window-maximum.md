# Sliding Window Maximum

## Problem Summary
Given an array of integers `nums` and a sliding window of size `k`, find the maximum element in each window as it moves from the beginning to the end of the array.

## Intuition
A naive approach would be to iterate through every window of size `k` and find the maximum, resulting in $O(N \times K)$ time complexity. To optimize this to $O(N)$, we need a way to efficiently keep track of the maximum element as the window slides. A **Monotonic Deque** (double-ended queue) is ideal for this because it allows us to maintain elements in a decreasing order, ensuring the maximum is always at the front.

## Approach
1. **Use a Deque**: Store indices of elements in the deque instead of values. This helps in checking if an index is still within the current window.
2. **Maintain Monotonicity**: 
   - Before inserting a new element `nums[i]`, remove all elements from the back of the deque that are smaller than or equal to `nums[i]`. These elements can never be the maximum for the current or future windows.
   - Insert the current index `i` at the back.
3. **Remove Out-of-Bounds Indices**: If the index at the front of the deque is less than `i - k + 1`, it's outside the current window and should be removed.
4. **Record Result**: Once the first window is fully processed (i.e., `i >= k - 1`), the element at the front of the deque is the maximum for the current window.

## Complexity
- **Time Complexity**: $O(N)$. Each element is pushed and popped from the deque at most once.
- **Space Complexity**: $O(K)$. The deque stores at most $k$ indices at any time.

## Pattern
- **Sliding Window + Monotonic Deque**: Useful for finding the maximum or minimum in a sliding window.

## Common Mistakes
- Not storing indices in the deque. Storing indices makes it easy to check if an element has fallen out of the window.
- Forgetting to remove smaller elements from the back before inserting the current one.
- Incorrect window boundary check (`i - k + 1`).

## Related Problems
- Sliding Window Minimum
- Longest Subarray with Absolute Diff Less Than or Equal to Limit
- Jump Game VI

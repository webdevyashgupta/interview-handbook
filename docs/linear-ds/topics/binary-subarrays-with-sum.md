# Binary Subarrays With Sum

## Problem Summary
Given a binary array `nums` (containing only `0`s and `1`s) and an integer `goal`, return the number of non-empty subarrays with a sum equal to `goal`.

## Intuition
This problem can be solved using the prefix sum hash map approach in $O(N)$ time and $O(N)$ space. However, since the array only contains non-negative integers (0 and 1), we can optimize the space complexity to $O(1)$ using a sliding window.

The difficulty with a direct sliding window for an *exact* sum is that zeros do not change the sum, making it hard to count all valid subarrays. A clever workaround is to use the property:
**`Count(Sum == goal) = Count(Sum <= goal) - Count(Sum <= goal - 1)`**

## Approach

### 1. Brute Force
- Iterate through all possible subarrays using two nested loops.
- Calculate the sum of each subarray.
- Increment the count if the sum equals `goal`.
- **Time Complexity:** $O(N^2)$.
- **Space Complexity:** $O(1)$.

### 2. Better (Prefix Sum + Hashing)
- Use a hash map to store the frequency of prefix sums encountered so far.
- For each element, calculate the current prefix sum `currSum`.
- The number of subarrays ending at the current index with sum `goal` is the frequency of `currSum - goal` in the map.
- **Time Complexity:** $O(N)$ (assuming $O(1)$ map operations).
- **Space Complexity:** $O(N)$.

### 3. Optimal (Sliding Window with "At Most" Helper)
- Define a helper function `countSubarraysWithSumAtMost(nums, k)`:
    - If `k < 0`, return `0`.
    - Use two pointers `L` and `R` to maintain a sliding window.
    - Keep track of the current window `sum`.
    - Expand `R` and add `nums[R]` to `sum`.
    - While `sum > k`, shrink the window from the left by subtracting `nums[L]` and moving `L`.
    - All subarrays ending at `R` within the valid window `[L, R]` have a sum $\le k$. There are exactly `(R - L + 1)` such subarrays.
    - Add `(R - L + 1)` to the total result at each step.
- The final result is: `countSubarraysWithSumAtMost(nums, goal) - countSubarraysWithSumAtMost(nums, goal - 1)`.

## Implementation

```python
def numSubarraysWithSum(nums, goal):
    """
    Counts subarrays with sum equal to goal.
    Optimal Approach: Sliding Window (At Most K - At Most K-1)
    Time Complexity: O(N), Space Complexity: O(1)
    """
    def countAtMost(k):
        if k < 0:
            return 0
        l = 0
        current_sum = 0
        count = 0
        for r in range(len(nums)):
            current_sum += nums[r]
            while current_sum > k:
                current_sum -= nums[l]
                l += 1
            # Number of subarrays ending at r with sum <= k
            count += (r - l + 1)
        return count

    return countAtMost(goal) - countAtMost(goal - 1)
```

## Complexity
- **Time Complexity:** $O(N)$. We perform two passes over the array, each pass being $O(N)$.
- **Space Complexity:** $O(1)$. We only use a few variables for pointers and sums.

## Pattern
- **Sliding Window (Helper function for "At Most K"):** Solving "Exactly K" by calculating "At Most K" and "At Most K-1".

## Common Mistakes
- **Handling Goal = 0:** Ensure the helper function handles `goal - 1` (which is -1) correctly by returning 0.
- **Counting Subarrays:** Remembering that `R - L + 1` counts the number of valid subarrays *ending at the current index R*.

## Related Problems
- [Count Number of Nice Subarrays](./count-number-of-nice-subarrays.md)
- [Subarrays with K Different Integers](./subarray-with-k-different-integers.md)
- [Count Subarray Sum Equals K](./count-subarray-sum-equals-k.md) (General version with negatives)

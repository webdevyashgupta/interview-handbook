# Kadane's Algorithm (Maximum Subarray Sum)

## Problem Summary
Given an array of integers, find the contiguous subarray (containing at least one number) which has the largest sum and return its sum.

## Intuition
- **Brute Force**: Generate all possible subarrays and calculate their sums individually.
- **Better**: Instead of re-calculating the sum for every subarray, notice that the sum of subarray `arr[i...j]` is just `sum(arr[i...j-1]) + arr[j]`. This reduces one loop.
- **Optimal (Kadane's)**: The logic is simple—don't carry a negative "burden". If the sum of the subarray we are currently building becomes negative, it will only decrease the sum of any future subarray it is joined with. In such cases, we discard the current subarray sum and start fresh from the next element.

## Approach

### Brute Force
- **Method**: Use three nested loops. Outer two loops define the range `[i, j]` and the third loop calculates the sum of that range.
- **Time Complexity**: $O(N^3)$
- **Space Complexity**: $O(1)$

### Better Approach
- **Method**: Use two nested loops. The outer loop sets the start index `i`, and the inner loop sets the end index `j`, calculating the sum incrementally.
- **Time Complexity**: $O(N^2)$
- **Space Complexity**: $O(1)$

### Optimal Approach (Kadane's Algorithm)
- **Method**:
    1. Initialize `max_sum = INT_MIN` and `current_sum = 0`.
    2. Traverse the array:
        - Add `arr[i]` to `current_sum`.
        - Update `max_sum = max(max_sum, current_sum)`.
        - If `current_sum < 0`, reset `current_sum = 0`.
- **Handling All-Negative Arrays**: If the problem allows an empty subarray, and all numbers are negative, the answer should be 0. If it requires at least one element, Kadane's will correctly return the maximum (least negative) element as long as you update `max_sum` before resetting `current_sum`.
- **Time Complexity**: $O(N)$
- **Space Complexity**: $O(1)$

### Printing the Maximum Subarray (Follow-up)
- **Method**: 
    - Maintain a `start` variable that updates to the current index `i` whenever `current_sum` is reset to 0.
    - Whenever `max_sum` is updated, store the current `start` in `ansStart` and the current index `i` in `ansEnd`.
    - The maximum subarray is `arr[ansStart...ansEnd]`.

## Implementation

```python
def maxSubArray(nums):
    """
    Finds the contiguous subarray with the largest sum.
    Optimal Approach: Kadane's Algorithm
    Time Complexity: O(N), Space Complexity: O(1)
    """
    # Initialize with a very small number
    max_sum = -float('inf')
    current_sum = 0
    
    for x in nums:
        current_sum += x
        
        # Update max_sum before resetting current_sum
        # (This handles cases where all numbers are negative)
        max_sum = max(max_sum, current_sum)
        
        # If sum becomes negative, discard it and start fresh
        if current_sum < 0:
            current_sum = 0
            
    return max_sum
```

## Complexity
- **Time**: $O(N)$ - A single pass through the array.
- **Space**: $O(1)$ - Only a few variables to store sums and indices.

## Pattern
- **Prefix Sums / Dynamic Programming**: Making a decision at each step based on the result of the previous step.

## Common Mistakes
- **Order of Operations**: Resetting `current_sum` to 0 *before* updating `max_sum` can lead to incorrect results if the maximum sum is negative.
- **Initial Max Value**: Initializing `max_sum` to 0 instead of `INT_MIN` when all array elements are negative.

## Related Problems
- Maximum Product Subarray.
- Smallest Subarray Sum.
- Flip bits to maximize number of 1s in a binary array.

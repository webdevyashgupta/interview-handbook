# Maximum Product Subarray

## Problem Summary
Given an integer array `nums`, find a contiguous non-empty subarray that has the largest product, and return the product.

## Intuition
Unlike Maximum Sum Subarray (Kadane's), the Maximum Product Subarray is tricky because:
1. All positive numbers increase the product.
2. A single zero resets the product to zero.
3. Negative numbers are the most complex: a single negative number makes the product negative, but a second negative number makes it positive again.

The observation-based approach realizes that the maximum product must involve either a prefix or a suffix of the array (or a segment between zeros).

## Approach

### Brute Force
- **Method**: Generate all subarrays using two nested loops. Calculate the product of each subarray.
- **Optimization**: Instead of a third loop to calculate product, maintain a `runningProduct`.
- **Time Complexity**: $O(N^2)$
- **Space Complexity**: $O(1)$

### Optimal (Observation Based)
- **Method**: Traverse the array from left to right (Prefix Product) and from right to left (Suffix Product).
- **Logic**:
    - The maximum product will likely be a prefix product or a suffix product.
    - **Handle Zeros**: If we encounter a zero, the product becomes zero. Since we want to find the max product of a contiguous subarray, we "reset" the product to 1 if it hits 0, effectively starting a new subarray search after the zero.
    - **Handle Negative Numbers**: 
        - If there's an even number of negatives, the product of the entire segment is positive.
        - If there's an odd number of negatives, removing one negative (either the first one from the suffix or the last one from the prefix) will leave an even number of negatives. Thus, the max product will be either the prefix up to the last negative or the suffix up to the first negative.
- **Algorithm**:
    1. Initialize `prefix = 1`, `suffix = 1`, `ans = -infinity`.
    2. Loop $i$ from 0 to $N-1$:
        - If `prefix == 0`, `prefix = 1`.
        - If `suffix == 0`, `suffix = 1`.
        - `prefix *= nums[i]`
        - `suffix *= nums[n - i - 1]`
        - `ans = max(ans, prefix, suffix)`
    3. Return `ans`.

## Complexity
- **Time**: $O(N)$ - Single pass (or two passes) through the array.
- **Space**: $O(1)$ - Only constant extra space for variables.

## Pattern
- **Prefix and Suffix Scans**: Useful for problems where the result depends on elements to the left or right, especially when symmetry is involved.

## Common Mistakes
- **Ignoring Zeros**: Forgetting to reset the product to 1 when a 0 is encountered.
- **Initial Max Value**: Initializing the maximum product to 0 instead of the first element or a very small negative number (important if the array contains only negative numbers).

## Related Problems
- Maximum Subarray (Kadane's Algorithm)
- Product of Array Except Self
- Subarray Sum Equals K

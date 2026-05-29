# Reverse Pairs

## Problem Summary
Given an array `nums`, return the number of reverse pairs in the array. A reverse pair is a pair $(i, j)$ such that $0 \le i < j < \text{nums.length}$ and $\text{nums}[i] > 2 \cdot \text{nums}[j]$.

## Intuition
This problem is a variation of "Count Inversions." Instead of just $\text{nums}[i] > \text{nums}[j]$, we need $\text{nums}[i] > 2 \cdot \text{nums}[j]$. While the standard Merge Sort inversion count logic doesn't directly apply during the merge step (because the condition $A[i] > 2 \cdot A[j]$ is different from the sorting condition $A[i] > A[j]$), we can still use the sorted nature of the subarrays to count pairs efficiently before the actual merge.

## Approach

### Brute Force
- **Method**: Use two nested loops to check every possible pair $(i, j)$.
- **Condition**: If $\text{nums}[i] > 2 \cdot \text{nums}[j]$ where $i < j$, increment the counter.
- **Time Complexity**: $O(N^2)$
- **Space Complexity**: $O(1)$

### Optimal (Merge Sort Modification)
- **Method**: Perform Merge Sort. Before merging the two sorted halves (`left` and `right`), count the reverse pairs.
- **Counting Logic**:
    - Use two pointers: $i$ pointing to the start of the `left` subarray and $j$ pointing to the start of the `right` subarray.
    - For each element in the `left` subarray ($i$ from `low` to `mid`):
        - Move pointer $j$ in the `right` subarray as long as $\text{nums}[i] > 2 \cdot \text{nums}[j]$.
        - All elements from the start of the `right` subarray up to $j-1$ will form a reverse pair with $\text{nums}[i]$.
        - Add $(j - (\text{mid} + 1))$ to the total count.
    - After counting, perform the standard merge step to maintain the sorted property.
- **Time Complexity**: $O(2N \log N)$ (Counting takes $O(N)$ and Merging takes $O(N)$ at each level).
- **Space Complexity**: $O(N)$ (for the temporary array).

## Complexity
- **Time**: $O(N \log N)$. The counting step is linear at each recursion level because the $j$ pointer only moves forward across all $i$ iterations (two-pointer technique).
- **Space**: $O(N)$ for the auxiliary array used during merging.

## Pattern
- **Divide and Conquer (Merge Sort)**: Leveraging the sorted property of subarrays to count specific pairs in linear time.

## Common Mistakes
- **Condition Order**: Ensure the counting is done *before* the merge step, otherwise, the relative order of elements changes.
- **Overflow**: When checking $\text{nums}[i] > 2 \cdot \text{nums}[j]$, $2 \cdot \text{nums}[j]$ can overflow a 32-bit integer. Cast to `long` before multiplication or use `nums[i] / 2.0 > nums[j]`.

## Related Problems
- Count Inversions
- Global and Local Inversions
- 3-Sum / 4-Sum

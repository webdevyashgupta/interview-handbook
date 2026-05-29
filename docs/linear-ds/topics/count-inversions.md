# Count Inversions in an Array

## Problem Summary
Given an array of integers, find the number of "inversions." A pair $(i, j)$ is called an inversion if $i < j$ and $A[i] > A[j]$. The goal is to return the total count of such pairs.

## Intuition
The problem asks us to count how many elements to the right of a given element are smaller than it. A simple brute force approach checks every pair, but we can optimize this by leveraging the Merge Sort algorithm. During the "merge" step of Merge Sort, we can efficiently count inversions because both halves are already sorted.

## Approach

### Brute Force
- **Method**: Use two nested loops. The outer loop picks an element $A[i]$, and the inner loop checks all elements $A[j]$ where $j > i$.
- **Condition**: If $A[i] > A[j]$, increment the counter.
- **Time Complexity**: $O(N^2)$
- **Space Complexity**: $O(1)$

### Optimal (Merge Sort Based)
- **Method**: Modify the Merge Sort algorithm.
- **Logic**:
    - Divide the array into two halves recursively until each subarray has one element.
    - During the merge step (merging two sorted subarrays `left` and `right`):
        - Maintain two pointers, $i$ for `left` and $j$ for `right`.
        - If `left[i] <= right[j]`, it's not an inversion. Move to the next element in `left`.
        - If `left[i] > right[j]`, then $A[i]$ and $A[j]$ form an inversion. Since `left` is sorted, **all** elements from index $i$ to the end of the `left` subarray will also be greater than `right[j]`.
        - **Count**: Add `(mid - i + 1)` to the total inversion count.
- **Time Complexity**: $O(N \log N)$
- **Space Complexity**: $O(N)$ (for the temporary array used in merging).

## Complexity
- **Time**: $O(N \log N)$ due to the Merge Sort structure.
- **Space**: $O(N)$ for the auxiliary array used during the merge process.

## Pattern
- **Merge Sort / Divide and Conquer**: Using the structure of a sorting algorithm to solve a counting problem on pairs.

## Common Mistakes
- **Integer Overflow**: The number of inversions can exceed the range of a 32-bit integer (e.g., in a reverse-sorted array of size $10^5$, the count is $\approx 5 \times 10^9$). Use `long long` (C++) or `long` (Java).
- **Not watch Merge Sort first**: Understanding the base Merge Sort algorithm is crucial for implementing this modification correctly.

## Related Problems
- Reverse Pairs
- Global and Local Inversions
- Number of Longest Increasing Subsequences

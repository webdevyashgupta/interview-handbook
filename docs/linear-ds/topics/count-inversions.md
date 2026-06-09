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

## Implementation

```python
def numberOfInversions(a, n):
    """
    Counts inversions in an array using Merge Sort.
    Time Complexity: O(N log N), Space Complexity: O(N)
    """
    def merge(arr, low, mid, high):
        temp = []
        left = low
        right = mid + 1
        cnt = 0
        
        while left <= mid and right <= high:
            if arr[left] <= arr[right]:
                temp.append(arr[left])
                left += 1
            else:
                # Inversion found! 
                # All elements from 'left' to 'mid' are > arr[right]
                temp.append(arr[right])
                cnt += (mid - left + 1)
                right += 1
                
        while left <= mid:
            temp.append(arr[left])
            left += 1
        while right <= high:
            temp.append(arr[right])
            right += 1
            
        for i in range(low, high + 1):
            arr[i] = temp[i - low]
            
        return cnt

    def merge_sort(arr, low, high):
        cnt = 0
        if low >= high:
            return cnt
        mid = (low + high) // 2
        cnt += merge_sort(arr, low, mid)
        cnt += merge_sort(arr, mid + 1, high)
        cnt += merge(arr, low, mid, high)
        return cnt

    return merge_sort(a, 0, n - 1)
```

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

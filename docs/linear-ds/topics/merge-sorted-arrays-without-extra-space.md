# Merge Sorted Arrays Without Extra Space

## Problem Summary
Given two sorted arrays `arr1[]` and `arr2[]` of sizes `n` and `m` respectively, merge them such that the first `n` smallest elements are in `arr1[]` and the remaining `m` elements are in `arr2[]`. All elements in both arrays should be sorted. No extra space should be used.

## Intuition
Since both arrays are already sorted, we can observe that if an element in `arr1` is larger than an element in `arr2`, they should swap places. By comparing elements from the end of `arr1` and the start of `arr2`, we can move smaller elements to `arr1` and larger ones to `arr2`.

## Approach

### 1. Brute Force
Use an auxiliary array to merge the two arrays.
- **Steps:**
    1. Create a third array `arr3` of size `n + m`.
    2. Use two pointers to merge `arr1` and `arr2` into `arr3` in sorted order.
    3. Copy the first `n` elements of `arr3` back to `arr1` and the remaining `m` elements back to `arr2`.
- **Time Complexity:** $O(n + m)$ for merging and copying.
- **Space Complexity:** $O(n + m)$ for the extra array.

### 2. Optimal Approach (Swap and Sort)
Use two pointers to swap misplaced elements and then sort.
- **Steps:**
    1. Set `left = n - 1` (end of `arr1`) and `right = 0` (start of `arr2`).
    2. While `left >= 0` and `right < m`:
        - If `arr1[left] > arr2[right]`:
            - Swap `arr1[left]` and `arr2[right]`.
            - `left--`, `right++`.
        - Else:
            - Break (since arrays are sorted, no more such pairs exist).
    3. Sort `arr1` and `arr2` individually.
- **Time Complexity:** $O(\min(n, m)) + O(n \log n) + O(m \log m)$.
- **Space Complexity:** $O(1)$ (excluding sorting stack space).

### 3. Optimal Approach (Gap Method)
Based on the Shell Sort algorithm.
- **Steps:**
    1. Calculate `gap = ceil((n + m) / 2)`.
    2. While `gap > 0`:
        - Use two pointers `i = 0` and `j = gap`.
        - Traverse the "virtual" combined array:
            - If `j < n`: Compare `arr1[i]` and `arr1[j]`.
            - If `i < n` and `j >= n`: Compare `arr1[i]` and `arr2[j - n]`.
            - If `i >= n`: Compare `arr2[i - n]` and `arr2[j - n]`.
            - If elements are in wrong order, swap them.
        - If `gap == 1`, break. Otherwise, `gap = ceil(gap / 2)`.
- **Time Complexity:** $O((n+m) \log(n+m))$.
- **Space Complexity:** $O(1)$.

## Implementation

```python
import math

def merge(arr1: list[int], n: int, arr2: list[int], m: int) -> None:
    gap = math.ceil((n + m) / 2)
    
    while gap > 0:
        i = 0
        j = gap
        
        while j < (n + m):
            # i in arr1, j in arr1
            if j < n:
                if arr1[i] > arr1[j]:
                    arr1[i], arr1[j] = arr1[j], arr1[i]
            # i in arr1, j in arr2
            elif i < n and j >= n:
                if arr1[i] > arr2[j - n]:
                    arr1[i], arr2[j - n] = arr2[j - n], arr1[i]
            # i in arr2, j in arr2
            elif i >= n:
                if arr2[i - n] > arr2[j - n]:
                    arr2[i - n], arr2[j - n] = arr2[j - n], arr2[i - n]
            
            i += 1
            j += 1
            
        if gap == 1:
            break
        gap = math.ceil(gap / 2)
```

## Complexity
- **Time Complexity:** $O((n+m) \log(n+m))$ for the Gap method.
- **Space Complexity:** $O(1)$.

## Pattern
- **Two Pointers**: Used to swap elements across arrays.
- **Gap Method (Shell Sort)**: Efficiently sorting elements without a full sort at every step.

## Common Mistakes
- **Using extra space**: Using a third array when the problem explicitly forbids it.
- **Index out of bounds**: When implementing the Gap method, carefully mapping virtual indices to `arr1` and `arr2`.

## Related Problems
- Merge Two Sorted Lists
- Median of Two Sorted Arrays
- Inplace Merge Sort

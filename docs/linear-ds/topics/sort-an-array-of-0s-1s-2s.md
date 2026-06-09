# Sort an Array of 0's, 1's, and 2's

## Problem Summary
Given an array consisting of only `0`s, `1`s, and `2`s, sort the array in-place so that all `0`s come first, followed by `1`s, and then `2`s.

## Intuition
The goal is to sort the array with minimal time and space. Since we know the array only contains three distinct values, we can move beyond standard sorting algorithms (like Merge Sort) and use frequency counting or a more efficient three-pointer approach.

## Approach

### Brute Force
- **Method**: Use any standard sorting algorithm like Merge Sort or Quick Sort.
- **Time Complexity**: $O(N \log N)$
- **Space Complexity**: $O(N)$ (for Merge Sort) or $O(1)$ (for Quick Sort)

### Better Approach (Counting Sort)
- **Method**:
    1. Iterate through the array once to count the number of `0`s, `1`s, and `2`s.
    2. Iterate again and overwrite the array: fill the first `count0` places with `0`, the next `count1` places with `1`, and the remaining with `2`.
- **Time Complexity**: $O(N) + O(N) \approx O(2N)$
- **Space Complexity**: $O(1)$ (only three variables needed for counting).

### Optimal Approach (Dutch National Flag Algorithm)
- **Method**: Use three pointers: `low`, `mid`, and `high`.
- **Logic**:
    - Everything from `0` to `low-1` should be `0`.
    - Everything from `low` to `mid-1` should be `1`.
    - Everything from `high+1` to `n-1` should be `2`.
    - The section from `mid` to `high` is unsorted.
- **Steps**:
    1. Initialize `low = 0`, `mid = 0`, `high = n-1`.
    2. While `mid <= high`:
        - If `arr[mid] == 0`: Swap `arr[low]` and `arr[mid]`, increment both `low` and `mid`.
        - If `arr[mid] == 1`: Increment `mid`.
        - If `arr[mid] == 2`: Swap `arr[mid]` and `arr[high]`, decrement `high`.
- **Time Complexity**: $O(N)$ (single pass)
- **Space Complexity**: $O(1)$

## Implementation

```python
def sortColors(nums: list[int]) -> None:
    low, mid, high = 0, 0, len(nums) - 1
    
    while mid <= high:
        if nums[mid] == 0:
            nums[low], nums[mid] = nums[mid], nums[low]
            low += 1
            mid += 1
        elif nums[mid] == 1:
            mid += 1
        else: # nums[mid] == 2
            nums[mid], nums[high] = nums[high], nums[mid]
            high -= 1
```

## Complexity
- **Time**: $O(N)$ - Only one iteration through the array.
- **Space**: $O(1)$ - In-place sorting without extra data structures.

## Pattern
- **Three Pointers**: Partitioning an array into three distinct sections.

## Common Mistakes
- **Incorrect pointer movement for 2**: When `arr[mid] == 2`, only decrement `high`. Do NOT increment `mid` because the element swapped from `high` to `mid` is unknown and must be processed in the next iteration.
- **Loop Condition**: Ensure the loop runs while `mid <= high` (including equality).

## Related Problems
- Sort Colors (LeetCode 75)
- Three-way partitioning of an array around a pivot.

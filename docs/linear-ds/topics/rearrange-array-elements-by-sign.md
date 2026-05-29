# Rearrange Array Elements by Sign

## Problem Summary
Rearrange the elements of an array such that positive and negative numbers appear alternately, starting with a positive number. The relative order of the positive and negative numbers must be preserved.

There are two varieties of this problem:
1. **Variety 1**: The number of positive and negative elements is equal ($n/2$ each).
2. **Variety 2**: The number of positive and negative elements may not be equal.

## Intuition
- **Variety 1**: Since we know exactly where each type of number should go (positives at $0, 2, 4 \dots$ and negatives at $1, 3, 5 \dots$), we can use two pointers to fill a new array in a single pass.
- **Variety 2**: We cannot use the fixed-index approach because we don't know when one type will run out. The most intuitive way is to separate positives and negatives and then merge them back.

## Approach

### Variety 1: Equal Positives and Negatives (Optimal)
- **Method**: 
    1. Initialize a result array `ans` of the same size as the input.
    2. Set `posIndex = 0` and `negIndex = 1`.
    3. Traverse the input array:
        - If `arr[i]` is positive, place it at `ans[posIndex]` and increment `posIndex` by 2.
        - If `arr[i]` is negative, place it at `ans[negIndex]` and increment `negIndex` by 2.
- **Time Complexity**: $O(N)$
- **Space Complexity**: $O(N)$ (to store the rearranged array).

### Variety 2: Unequal Positives and Negatives
- **Method**:
    1. Create two lists: `positives` and `negatives`.
    2. Traverse the input array and separate elements into these lists while maintaining their relative order.
    3. Compare the sizes of the two lists.
    4. Fill the original array alternately using both lists until the smaller list is exhausted.
    5. Append the remaining elements of the larger list to the end of the array.
- **Time Complexity**: $O(N)$ for separation + $O(N)$ for merging = $O(2N)$.
- **Space Complexity**: $O(N)$ (to store the separate lists).

## Complexity
- **Time**: $O(N)$ - Usually one or two linear passes.
- **Space**: $O(N)$ - Required to store the rearranged elements (since in-place rearrangement with preserved order and $O(1)$ space is complex/not possible for Variety 1).

## Pattern
- **Two Pointers**: Using separate pointers for different categories (positive/negative positions).
- **Index Manipulation**: Specifically using `+2` to maintain alternate positions.

## Common Mistakes
- **Relative Order**: Forgetting that the problem often requires preserving the original relative order of elements.
- **Index Overflow**: Not correctly handling the stopping condition when one list finishes in Variety 2.

## Related Problems
- Sort Array by Parity (Even/Odd).
- Move Zeros to end (maintaining relative order).

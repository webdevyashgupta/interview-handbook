# Arrays: Rotations, Moving Zeros, Union & Intersection

## 🧩 Problem Summary
This session covers several array manipulation and search problems:
1. **Rotate Array by K (or D) Places**: Shift elements of an array to the left or right by `K` positions.
2. **Move Zeros to End**: Push all zeros in the array to the end while maintaining the relative order of non-zero elements.
3. **Linear Search**: Find the first occurrence of an element in an array.
4. **Union of Two Sorted Arrays**: Find all unique elements from two sorted arrays.
5. **Intersection of Two Sorted Arrays**: Find common elements between two sorted arrays.

- **Input**: One or two arrays of integers.
- **Output**: Modified array or a new resulting array/index.
- **Constraints**: Arrays are often sorted (for Union/Intersection).

## 💡 Intuition
- **Rotations**: Rotating by `D` places is equivalent to rotating by `D % N`. A naive approach uses extra space, but the optimal approach uses array reversal.
- **Move Zeros**: Use a two-pointer approach to "collect" non-zero elements at the beginning.
- **Union/Intersection**: Leverage the sorted property using two pointers to avoid the $O(N \log N)$ cost of a Set or nested loops.

## 🔁 Approach

### 1. Brute Force
- **Rotate by D**: Copy the first `D` elements into a temporary array, shift the rest of the array, and then put the `D` elements back at the end.
- **Union**: Use a `Set` to store elements from both arrays. The set automatically handles duplicates.
- **Intersection**: For each element in `arr1`, search it in `arr2`. If found and not already "visited," add to result.

### 2. Better Approach (Rotate Array)
- For rotation, using a temporary array of size `D` is the "better" approach compared to rotating by 1 place `D` times.

### 3. Optimal Approach
- **Rotate by D (Reversal Algorithm)**: 
  1. Reverse the first `D` elements.
  2. Reverse the remaining `N-D` elements.
  3. Reverse the whole array.
- **Move Zeros**: Use pointer `j` to find the first zero. Then use pointer `i` from `j+1` to find non-zeros and swap with `j`.
- **Union (Two Pointers)**: Compare `arr1[i]` and `arr2[j]`. Add the smaller one to the result if it's not the same as the last added element.
- **Intersection (Two Pointers)**: If `arr1[i] == arr2[j]`, add to result and increment both. If `arr1[i] < arr2[j]`, increment `i`, else increment `j`.

```python
# Rotate Left by D (Optimal)
def rotate_left(arr, d):
    n = len(arr)
    d %= n
    def reverse(l, r):
        while l < r:
            arr[l], arr[r] = arr[r], arr[l]
            l += 1; r -= 1
    reverse(0, d-1)
    reverse(d, n-1)
    reverse(0, n-1)

# Move Zeros to End (Optimal)
def move_zeros(arr):
    n = len(arr)
    j = -1
    for i in range(n):
        if arr[i] == 0:
            j = i
            break
    if j == -1: return arr
    for i in range(j + 1, n):
        if arr[i] != 0:
            arr[i], arr[j] = arr[j], arr[i]
            j += 1

# Union of Sorted Arrays (Optimal)
def find_union(arr1, arr2):
    i, j = 0, 0
    union = []
    while i < len(arr1) and j < len(arr2):
        if arr1[i] <= arr2[j]:
            if not union or union[-1] != arr1[i]:
                union.append(arr1[i])
            i += 1
        else:
            if not union or union[-1] != arr2[j]:
                union.append(arr2[j])
            j += 1
    while i < len(arr1):
        if union[-1] != arr1[i]: union.append(arr1[i])
        i += 1
    while j < len(arr2):
        if union[-1] != arr2[j]: union.append(arr2[j])
        j += 1
    return union
```

## Implementation

```python
def rotate_left_optimal(arr, d):
    """
    Rotates array to the left by D places using reversal algorithm.
    Time: O(N), Space: O(1)
    """
    n = len(arr)
    if n == 0: return
    d %= n
    def reverse(l, r):
        while l < r:
            arr[l], arr[r] = arr[r], arr[l]
            l += 1; r -= 1
    reverse(0, d-1)
    reverse(d, n-1)
    reverse(0, n-1)

def move_zeros_optimal(arr):
    """
    Moves all zeros to the end while maintaining relative order.
    Time: O(N), Space: O(1)
    """
    n = len(arr)
    j = -1
    # Find the first zero
    for i in range(n):
        if arr[i] == 0:
            j = i
            break
    if j == -1: return arr
    
    # Swap non-zeros with the first available zero
    for i in range(j + 1, n):
        if arr[i] != 0:
            arr[i], arr[j] = arr[j], arr[i]
            j += 1
    return arr

def find_union_optimal(arr1, arr2):
    """
    Finds union of two sorted arrays using two pointers.
    Time: O(N1 + N2), Space: O(1) (excluding result)
    """
    i, j = 0, 0
    union = []
    while i < len(arr1) and j < len(arr2):
        if arr1[i] <= arr2[j]:
            if not union or union[-1] != arr1[i]:
                union.append(arr1[i])
            i += 1
        else:
            if not union or union[-1] != arr2[j]:
                union.append(arr2[j])
            j += 1
    while i < len(arr1):
        if not union or union[-1] != arr1[i]:
            union.append(arr1[i])
        i += 1
    while j < len(arr2):
        if not union or union[-1] != arr2[j]:
            union.append(arr2[j])
        j += 1
    return union

def find_intersection_optimal(arr1, arr2):
    """
    Finds intersection of two sorted arrays using two pointers.
    Time: O(N1 + N2), Space: O(1) (excluding result)
    """
    i, j = 0, 0
    intersection = []
    while i < len(arr1) and j < len(arr2):
        if arr1[i] < arr2[j]:
            i += 1
        elif arr2[j] < arr1[i]:
            j += 1
        else:
            if not intersection or intersection[-1] != arr1[i]:
                intersection.append(arr1[i])
            i += 1
            j += 1
    return intersection
```

## ⏱️ Complexity
- **Time**: 
  - Rotation/Move Zeros: $O(N)$
  - Union/Intersection: $O(N1 + N2)$
- **Space**: 
  - Rotation (Optimal): $O(1)$
  - Move Zeros: $O(1)$
  - Union (Optimal): $O(N1 + N2)$ for returning the result, $O(1)$ extra space.

## 📌 Pattern
- **Two Pointers**: Heavily used for in-place swaps (Move Zeros) and merging (Union/Intersection).
- **Array Reversal**: A powerful trick for rotation.

## ⚠️ Common Mistakes
- **Rotation**: Forgetting `d = d % n`. If `d` is larger than `n`, it leads to unnecessary work or errors.
- **Union**: Not checking `union[-1] != current` before appending, leading to duplicates.
- **Move Zeros**: Not finding the first zero before starting the swap loop, leading to redundant swaps.

## 🔗 Related Problems
- Right Rotate Array by K places
- Merge Sorted Arrays without extra space
- Find common elements in three sorted arrays

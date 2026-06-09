# Array Basics: Second Largest & Remove Duplicates

## 🧩 Problem Summary
This topic covers fundamental array operations including:
1. **Find Largest Element**: Given an array, find the maximum element.
2. **Find Second Largest Element**: Find the second largest distinct element without sorting (optimal).
3. **Check if Sorted**: Verify if an array is sorted in non-descending order.
4. **Remove Duplicates**: Remove duplicates from a sorted array in-place and return the number of unique elements.

- **Input**: An array of integers `arr`.
- **Output**: Varies (max value, second max value, boolean, or modified array + length).
- **Constraints**: 
  - Array size $N \ge 1$ (or $N \ge 2$ for second largest).
  - Elements can be positive or negative.

## 💡 Intuition
- **Largest/Second Largest**: The core idea is to maintain a "current best" (largest) and "runner-up" (second largest) while scanning the array once. If we find a new winner, the old winner becomes the runner-up.
- **Sorted Check**: A sorted array must satisfy $arr[i] \ge arr[i-1]$ for all $i > 0$.
- **Remove Duplicates**: Since the array is sorted, all duplicates are adjacent. We can use a "slow" pointer to track the position of unique elements and a "fast" pointer to find the next unique value.

## 🔁 Approach

### 1. Brute Force (Second Largest & Duplicates)
- **Second Largest**: Sort the array in ascending order. The largest is at $arr[N-1]$. Traverse backwards to find the first element not equal to the largest.
- **Remove Duplicates**: Use a `Set` data structure to store unique elements. Then, iterate through the set and overwrite the original array's first few positions.
```python
# Second Largest Brute Force
def second_largest_brute(arr):
    arr.sort()
    largest = arr[-1]
    for i in range(len(arr)-2, -1, -1):
        if arr[i] != largest:
            return arr[i]
    return -1

# Remove Duplicates Brute Force
def remove_duplicates_brute(arr):
    st = set(arr)
    unique_list = sorted(list(st))
    for i in range(len(unique_list)):
        arr[i] = unique_list[i]
    return len(unique_list)
```

### 2. Better Approach (Second Largest)
- **Second Largest**: Perform two passes. In the first pass, find the `largest`. In the second pass, find the maximum element that is not equal to `largest`.
```python
def second_largest_better(arr):
    largest = -float('inf')
    for x in arr:
        largest = max(largest, x)
    
    s_largest = -float('inf')
    for x in arr:
        if x > s_largest and x != largest:
            s_largest = x
    return s_largest if s_largest != -float('inf') else -1
```

### 3. Optimal Approach (One-Pass / Two-Pointers)
- **Second Largest**: One-pass scan. Compare current element `arr[i]` with `largest`. If `arr[i] > largest`, update `s_largest = largest` and `largest = arr[i]`. If `arr[i] < largest` but `arr[i] > s_largest`, update `s_largest`.
- **Remove Duplicates**: Use two pointers `i` (slow) and `j` (fast). If `arr[j] != arr[i]`, move `i` forward and set `arr[i] = arr[j]`.
```python
# Second Largest Optimal
def second_largest_optimal(arr):
    largest = arr[0]
    s_largest = -1
    for i in range(1, len(arr)):
        if arr[i] > largest:
            s_largest = largest
            largest = arr[i]
        elif arr[i] < largest and arr[i] > s_largest:
            s_largest = arr[i]
    return s_largest

# Remove Duplicates Optimal (Two-Pointer)
def remove_duplicates_optimal(arr):
    if not arr: return 0
    i = 0
    for j in range(1, len(arr)):
        if arr[j] != arr[i]:
            i += 1
            arr[i] = arr[j]
    return i + 1
```

## Implementation

```python
def second_largest_optimal(arr):
    """
    Finds the second largest distinct element in one pass.
    """
    if len(arr) < 2:
        return -1
    
    largest = arr[0]
    s_largest = -float('inf')
    
    for i in range(1, len(arr)):
        if arr[i] > largest:
            s_largest = largest
            largest = arr[i]
        elif arr[i] < largest and arr[i] > s_largest:
            s_largest = arr[i]
            
    return s_largest if s_largest != -float('inf') else -1

def remove_duplicates_optimal(arr):
    """
    Removes duplicates from a sorted array in-place.
    Returns the number of unique elements.
    """
    if not arr:
        return 0
    
    i = 0 # Slow pointer
    for j in range(1, len(arr)): # Fast pointer
        if arr[j] != arr[i]:
            i += 1
            arr[i] = arr[j]
            
    return i + 1

def check_sorted(arr):
    """
    Checks if the array is sorted in non-descending order.
    """
    for i in range(1, len(arr)):
        if arr[i] < arr[i-1]:
            return False
    return True
```

## ⏱️ Complexity
- **Time**: 
  - Brute Force: $O(N \log N)$ (due to sorting).
  - Better/Optimal: $O(N)$ (single or double pass).
- **Space**: 
  - Brute Force: $O(N)$ (for set or sorting space).
  - Optimal: $O(1)$ (in-place).

## 📌 Pattern
- **Arrays**: Iteration and Comparisons.
- **Two Pointers**: Used in "Remove Duplicates" to modify the array in-place.

## ⚠️ Common Mistakes
- **Second Largest**: Initializing `s_largest` with 0 if the array can contain negative numbers (use `-infinity` instead).
- **Duplicates**: Forgetting that the array is already sorted; people often jump to using a Hash Set which uses extra $O(N)$ space.
- **Index Out of Bounds**: When checking `arr[i-1]`, ensure the loop starts from index 1.

## 🔗 Related Problems
- Third Largest Element
- Move Zeros to End
- Rotate Array by K places

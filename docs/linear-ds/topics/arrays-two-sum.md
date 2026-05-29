# Arrays: 2-Sum Problem

## 🧩 Problem Summary
The 2-Sum problem has two common varieties:
- **Variety 1**: Return `YES` if there exist two numbers in the array that sum up to a target `K`, else return `NO`.
- **Variety 2**: Return the **indices** of the two numbers that sum up to `K`.

- **Input**: An array of integers `arr` and a target integer `K`.
- **Output**: Boolean (Variety 1) or a pair of indices (Variety 2).
- **Constraints**: $N$ up to $10^5$, elements can be positive or negative.

## 💡 Intuition
- **Brute Force**: Check every possible pair of elements. If their sum equals `K`, we found our answer.
- **Hashing (Better)**: As we iterate, we know exactly what value we are looking for: `rem = Target - current`. We can use a hash map to check if `rem` has been seen before in $O(1)$ time.
- **Two Pointers (Optimal for space)**: If the array is sorted, we can use one pointer at the start and one at the end. If the sum is too small, move the left pointer; if too large, move the right pointer.

## 🔁 Approach

### 1. Brute Force
- Use two nested loops to check all pairs $(i, j)$ where $i \neq j$.
```python
def two_sum_brute(arr, target):
    n = len(arr)
    for i in range(n):
        for j in range(i + 1, n):
            if arr[i] + arr[j] == target:
                return [i, j] # or True
    return [] # or False
```

### 2. Better Approach (Hashing)
- Use a dictionary (hash map) to store `element: index`.
- For each element, calculate the required complement. If it's in the map, return the current index and the stored index.
```python
def two_sum_hashing(arr, target):
    mpp = {}
    for i in range(len(arr)):
        num = arr[i]
        more = target - num
        if more in mpp:
            return [mpp[more], i] # Returns indices
        mpp[num] = i
    return []
```

### 3. Optimal Approach (Two Pointers)
- **Note**: This is optimal for **Variety 1** (Yes/No) as it uses $O(1)$ extra space if we don't count the sorting. For **Variety 2**, hashing is usually preferred because sorting loses the original indices.
- Sort the array first.
- Use `left = 0` and `right = n-1`.
```python
def two_sum_optimal(arr, target):
    arr.sort() # O(N log N)
    left = 0
    right = len(arr) - 1
    while left < right:
        sum_val = arr[left] + arr[right]
        if sum_val == target:
            return "YES"
        elif sum_val < target:
            left += 1
        else:
            right -= 1
    return "NO"
```

## ⏱️ Complexity
- **Time**: 
  - Brute Force: $O(N^2)$
  - Hashing: $O(N)$ (Average case with `unordered_map`)
  - Two Pointers: $O(N \log N)$ (due to sorting)
- **Space**: 
  - Brute Force: $O(1)$
  - Hashing: $O(N)$ (to store the map)
  - Two Pointers: $O(1)$ or $O(N)$ depending on sorting implementation.

## 📌 Pattern
- **Hashing**: Use to find complements in constant time.
- **Two Pointers / Greedy**: Used on sorted arrays to narrow down the search space.

## ⚠️ Common Mistakes
- **Same Element**: Ensuring you don't use the same element twice (e.g., if target is 6 and array has 3 at index 0, don't return `[0, 0]`).
- **Sorting Indices**: If the problem asks for indices (Variety 2), sorting the array directly will destroy the original index mapping. You'd need to store pairs of `(value, index)` before sorting.
- **Map Selection**: In C++, using `map` (ordered) is $O(N \log N)$ total, while `unordered_map` is $O(N)$ on average but can be $O(N^2)$ in worst-case collisions.

## 🔗 Related Problems
- 3-Sum Problem
- 4-Sum Problem
- Count pairs with given sum

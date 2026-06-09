# Arrays: Longest Subarray with Sum K

## 🧩 Problem Summary
- **Problem**: Find the length of the longest contiguous subarray whose elements sum up to a given value `K`.
- **Variations**:
  1. Array contains only positives and zeros.
  2. Array contains positives, negatives, and zeros.
- **Input**: An array `arr` and an integer `K`.
- **Output**: Length of the longest subarray.
- **Constraints**: $N$ up to $10^5$, $K$ up to $10^9$.

## 💡 Intuition
- **Subarray vs Subsequence**: A subarray is a **contiguous** part of an array.
- **Prefix Sum Strategy**: If the sum from the start to index `i` is `x`, and we are looking for a sum `K`, we check if a sum of `x - K` existed at some previous index `j`. If it did, the subarray from `j+1` to `i` has sum `K`.
- **Two Pointers (Positives only)**: If the array only has non-negatives, we can use a sliding window. Increasing the window size always increases the sum, and decreasing it always decreases the sum.

## 🔁 Approach

### 1. Brute Force
- Generate all possible subarrays using two nested loops (`i` from $0$ to $n-1$ and `j` from `i` to $n-1$).
- For each subarray `(i, j)`, calculate the sum using a third loop or a running sum.
```python
# Brute Force O(N^2)
def longest_subarray_brute(arr, k):
    n = len(arr)
    max_len = 0
    for i in range(n):
        current_sum = 0
        for j in range(i, n):
            current_sum += arr[j]
            if current_sum == k:
                max_len = max(max_len, j - i + 1)
    return max_len
```

### 2. Better Approach (Hashing / Prefix Sum)
- Use a hash map to store the first occurrence of each prefix sum.
- For each index `i`, calculate current prefix `sum`. Check if `sum - k` exists in the map.
- **Crucial for Zeros**: Only add the `sum` to the map if it doesn't already exist (to keep the leftmost index and maximize length).
```python
# Hashing O(N) or O(N log N), Space O(N)
# Optimal for arrays with negatives
def longest_subarray_hashing(arr, k):
    pre_sum_map = {}
    current_sum = 0
    max_len = 0
    for i in range(len(arr)):
        current_sum += arr[i]
        if current_sum == k:
            max_len = max(max_len, i + 1)
        
        rem = current_sum - k
        if rem in pre_sum_map:
            max_len = max(max_len, i - pre_sum_map[rem])
            
        if current_sum not in pre_sum_map:
            pre_sum_map[current_sum] = i
    return max_len
```

### 3. Optimal Approach (Two Pointers / Sliding Window)
- **Applicable only for positives/zeros**.
- Maintain two pointers `left` and `right`.
- Expand `right` and add `arr[right]` to `sum`.
- While `sum > k`, subtract `arr[left]` and increment `left`.
- If `sum == k`, update `max_len`.
```python
# Two Pointers O(2N) ~ O(N), Space O(1)
def longest_subarray_optimal(arr, k):
    left, right = 0, 0
    current_sum = arr[0]
    max_len = 0
    n = len(arr)
    while right < n:
        while left <= right and current_sum > k:
            current_sum -= arr[left]
            left += 1
        if current_sum == k:
            max_len = max(max_len, right - left + 1)
        right += 1
        if right < n:
            current_sum += arr[right]
    return max_len
```

## Implementation

```python
def longest_subarray_positives_optimal(arr, k):
    """
    Optimal for arrays with only positives and zeros.
    Uses Sliding Window (Two Pointers).
    """
    left, right = 0, 0
    current_sum = arr[0]
    max_len = 0
    n = len(arr)
    
    while right < n:
        while left <= right and current_sum > k:
            current_sum -= arr[left]
            left += 1
        if current_sum == k:
            max_len = max(max_len, right - left + 1)
        right += 1
        if right < n:
            current_sum += arr[right]
    return max_len

def longest_subarray_negatives_optimal(arr, k):
    """
    Optimal for arrays with positives, negatives, and zeros.
    Uses Prefix Sum + Hashing.
    """
    pre_sum_map = {}
    current_sum = 0
    max_len = 0
    
    for i in range(len(arr)):
        current_sum += arr[i]
        
        if current_sum == k:
            max_len = max(max_len, i + 1)
            
        rem = current_sum - k
        if rem in pre_sum_map:
            max_len = max(max_len, i - pre_sum_map[rem])
            
        if current_sum not in pre_sum_map:
            pre_sum_map[current_sum] = i
            
    return max_len
```

## ⏱️ Complexity
- **Time**: 
  - Hashing: $O(N)$ (with hash map) or $O(N \log N)$ (with ordered map).
  - Two Pointers: $O(N)$ (each pointer moves at most $N$ times).
- **Space**: 
  - Hashing: $O(N)$ for the map.
  - Two Pointers: $O(1)$.

## 📌 Pattern
- **Sliding Window**: Used when the sum is monotonic (positives only).
- **Prefix Sum + Hashing**: Standard technique for "subarray sum" problems, especially with negatives.

## ⚠️ Common Mistakes
- **Negatives/Zeros**: Two-pointer approach fails if there are negative numbers because shrinking the window doesn't necessarily decrease the sum.
- **Hashing Update**: In the hash map approach, updating the index for an existing prefix sum will give the *shortest* subarray instead of the *longest*.
- **Initial Sum**: Forgetting to check if the `current_sum` itself equals `K`.

## 🔗 Related Problems
- Subarray Sum Equals K (Count of subarrays)
- Longest Subarray with sum divisible by K
- Largest subarray with 0 sum

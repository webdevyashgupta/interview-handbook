# Fruit Into Baskets

## Problem Summary
You are visiting a farm that has a single row of fruit trees from left to right. Each tree has a type of fruit. You have two baskets, and each basket can only carry a single type of fruit. You want to collect as many fruits as possible, but you must stop if you encounter a third type of fruit. You must collect fruits in a contiguous manner (no skipping trees).

This problem is equivalent to: **Find the length of the longest subarray with at most two distinct elements.**

## Intuition
Since we need to find the maximum length of a contiguous segment (subarray) with a specific constraint (at most two unique types), this is a classic sliding window problem. We expand our window as long as we have $\le 2$ types of fruits and shrink it when we hit a third type.

## Approach

### 1. Brute Force
- **Idea**: Generate all possible subarrays. For each subarray, use a set to count the number of unique fruit types.
- **Optimization**: For each starting index `i`, expand `j` and keep track of unique fruits. Stop when unique fruits > 2.
- **Time Complexity**: $O(N^2)$
- **Space Complexity**: $O(1)$ (since the set size is at most 3).

### 2. Better Approach (Sliding Window - Standard)
- **Idea**: Use two pointers, `left` and `right`, and a hash map to store the frequency of each fruit type in the current window.
- **Steps**:
    1. Initialize `left = 0`, `right = 0`, `max_len = 0`, and an empty map.
    2. Iterate with `right` pointer.
    3. Add `fruits[right]` to the map (increment its count).
    4. While `map.size() > 2`:
        - Decrement the count of `fruits[left]` in the map.
        - If the count becomes 0, remove the fruit type from the map.
        - Increment `left`.
    5. Update `max_len = max(max_len, right - left + 1)`.
- **Time Complexity**: $O(2N)$
- **Space Complexity**: $O(1)$ (map size is at most 3).

### 3. Optimal Approach (Sliding Window - $O(N)$)
- **Idea**: Similar to Max Consecutive Ones III, we don't need a `while` loop. We can just use an `if` to move `left` by one when the condition is violated. This keeps the window size stable and only allows it to grow.
- **Steps**:
    1. Initialize `left = 0`, `right = 0`, `max_len = 0`, and an empty map.
    2. Iterate with `right`.
    3. Add `fruits[right]` to the map.
    4. If `map.size() > 2`:
        - Decrement the count of `fruits[left]` and remove if it reaches 0.
        - Increment `left`.
    5. If `map.size() <= 2`, update `max_len = max(max_len, right - left + 1)`.
- **Time Complexity**: $O(N)$
- **Space Complexity**: $O(1)$

## Implementation

```python
def totalFruit(fruits):
    """
    Finds the length of the longest subarray with at most two distinct fruit types.
    Optimal Approach: Sliding Window (Grow-only version)
    Time Complexity: O(N), Space Complexity: O(1)
    """
    left = 0
    max_len = 0
    # Map to store frequency of fruit types in current window
    count = {}
    
    for right in range(len(fruits)):
        # Add fruit at right pointer
        count[fruits[right]] = count.get(fruits[right], 0) + 1
        
        # If we have more than 2 types, shrink window from left
        if len(count) > 2:
            count[fruits[left]] -= 1
            if count[fruits[left]] == 0:
                del count[fruits[left]]
            left += 1
            
        # If window is valid, update max_len
        # (In the grow-only version, we only update if it's currently valid)
        if len(count) <= 2:
            max_len = max(max_len, right - left + 1)
            
    return max_len
```

## Complexity
- **Time Complexity**: $O(N)$ for the optimal sliding window.
- **Space Complexity**: $O(1)$ since we store at most 3 types in the map.

## Pattern
- **Sliding Window (At most K distinct elements)**: This is a direct application of the "at most K" constraint where $K=2$.

## Common Mistakes
- **Map size vs. Frequency**: Confusing the number of unique elements (map size) with the total number of elements in the window.
- **Not removing from map**: Forgetting to remove a key from the map when its frequency reaches 0, which would leave `map.size()` incorrect.

## Related Problems
- Longest Substring with At Most K Distinct Characters
- Max Consecutive Ones III
- Subarrays with K Different Integers

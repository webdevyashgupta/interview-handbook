# Longest Substring with At Most K Distinct Characters

## Problem Summary
Given a string `s` and an integer `k`, find the length of the longest substring of `s` that contains at most `k` distinct characters.

## Intuition
This is a sliding window problem where we need to maintain a window containing $\le k$ unique characters. As we expand the window to the right, we keep track of the character frequencies. If the number of unique characters exceeds `k`, we shrink the window from the left until we satisfy the condition again.

## Approach

### 1. Brute Force
- **Idea**: Generate all possible substrings and for each, count the number of distinct characters using a hash map or a frequency array.
- **Optimization**: For each starting position `i`, expand `j` and keep updating the distinct character count. Break as soon as the count exceeds `k`.
- **Time Complexity**: $O(N^2)$
- **Space Complexity**: $O(256)$ or $O(k)$

### 2. Better Approach (Sliding Window - Standard)
- **Idea**: Use two pointers `left` and `right` and a hash map to maintain the frequency of characters in the current window.
- **Steps**:
    1. Initialize `left = 0`, `right = 0`, `max_len = 0`, and a map.
    2. Iterate with `right` through the string.
    3. Add `s[right]` to the map.
    4. While `map.size() > k`:
        - Decrement the count of `s[left]` in the map.
        - If the count becomes 0, remove the character from the map.
        - Increment `left`.
    5. Update `max_len = max(max_len, right - left + 1)`.
- **Time Complexity**: $O(2N)$
- **Space Complexity**: $O(k)$

### 3. Optimal Approach (Sliding Window - $O(N)$)
- **Idea**: To achieve $O(N)$, we don't shrink the window fully with a `while` loop. Instead, if the condition is violated, we move `left` forward by exactly one. This ensures the window size never decreases, effectively tracking the maximum valid window seen so far.
- **Steps**:
    1. Initialize `left = 0`, `right = 0`, `max_len = 0`, and a map.
    2. Iterate with `right`.
    3. Add `s[right]` to the map.
    4. If `map.size() > k`:
        - Decrement `s[left]` and remove if count is 0.
        - Increment `left`.
    5. If `map.size() <= k`, update `max_len = max(max_len, right - left + 1)`.
- **Time Complexity**: $O(N)$
- **Space Complexity**: $O(k)$

## Implementation

```python
def lengthOfLongestSubstringKDistinct(s: str, k: int) -> int:
    if k == 0: return 0
    count = {}
    l = 0
    max_len = 0
    
    for r in range(len(s)):
        count[s[r]] = count.get(s[r], 0) + 1
        
        if len(count) > k:
            count[s[l]] -= 1
            if count[s[l]] == 0:
                del count[s[l]]
            l += 1
            
        max_len = max(max_len, r - l + 1)
        
    return max_len
```

## Complexity
- **Time Complexity**: $O(N)$ for the optimal sliding window.
- **Space Complexity**: $O(min(N, 256))$ for the hash map.

## Pattern
- **Sliding Window (At most K distinct items)**: A standard pattern where we use a frequency map to count unique items within the current window.

## Common Mistakes
- **Map size**: Forgetting that `map.size()` is the indicator of distinct characters, not the sum of frequencies.
- **Removing keys**: Forgetting to `erase` the key from the map when its frequency reaches 0. If you don't erase it, `map.size()` will stay the same.

## Related Problems
- Longest Substring Without Repeating Characters
- Fruit Into Baskets (K=2)
- Max Consecutive Ones III

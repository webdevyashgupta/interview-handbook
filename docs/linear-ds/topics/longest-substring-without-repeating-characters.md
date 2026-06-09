# Longest Substring Without Repeating Characters

## Problem Summary
Given a string `s`, find the length of the longest substring without repeating characters. A substring is a contiguous sequence of characters within a string.

## Intuition
To find the longest substring without repeats, we can use a "window" that expands as we find new characters and shrinks or jumps when we encounter a character we've already seen within that window. The goal is to keep the window as large as possible while maintaining the "no duplicates" property.

## Approach

### 1. Brute Force
- **Idea**: Generate all possible substrings and check each one for repeating characters using a frequency array or hash set.
- **Optimization**: For each starting position `i`, expand the end position `j` until a duplicate is found.
- **Time Complexity**: $O(N^2)$, where $N$ is the length of the string.
- **Space Complexity**: $O(256)$ or $O(1)$ if we use a fixed-size frequency array for ASCII characters.

### 2. Optimal Approach (Sliding Window with Map)
- **Idea**: Use two pointers, `left` and `right`, to define a sliding window. We also use a hash map (or array) to store the last seen index of each character.
- **Steps**:
    1. Initialize `left = 0`, `right = 0`, and `max_len = 0`.
    2. Iterate through the string using the `right` pointer.
    3. If the character at `right` is already in the map:
        - Check if its last seen index is greater than or equal to `left`.
        - If yes, move `left` to `map[s[right]] + 1`. This effectively "jumps" the window past the first occurrence of the repeating character.
    4. Update the character's last seen index in the map to the current `right`.
    5. Calculate the current window length: `right - left + 1`.
    6. Update `max_len = max(max_len, current_length)`.
    7. Move the `right` pointer forward.

## Implementation

```python
def lengthOfLongestSubstring(s: str) -> int:
    char_map = {}
    l = 0
    max_len = 0
    
    for r in range(len(s)):
        if s[r] in char_map and char_map[s[r]] >= l:
            l = char_map[s[r]] + 1
            
        char_map[s[r]] = r
        max_len = max(max_len, r - l + 1)
        
    return max_len
```

## Complexity
- **Time Complexity**: $O(N)$, where $N$ is the length of the string. Each pointer (`left` and `right`) travels at most $N$ steps.
- **Space Complexity**: $O(min(N, 256))$ for the hash map/array used to store the last seen indices.

## Pattern
- **Two Pointers / Sliding Window (Dynamic Window Size)**: This is a classic example where the window size changes dynamically based on the characters encountered.

## Common Mistakes
- **Incorrect `left` pointer update**: Moving `left` back or not checking if the previously seen character is actually inside the current window (`map[s[right]] >= left`).
- **Length calculation**: Using `right - left` instead of `right - left + 1`.
- **Initialization**: Forgetting to initialize the hash map with a value that indicates the character hasn't been seen (e.g., -1).

## Related Problems
- Longest Substring with At Most K Distinct Characters
- Fruit Into Baskets
- Max Consecutive Ones III

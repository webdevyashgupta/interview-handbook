# Number of Substrings Containing All Three Characters

## Problem Summary
Given a string `s` consisting only of characters 'a', 'b', and 'c', return the number of substrings that contain at least one occurrence of all three characters.

## Intuition
A substring is valid if it contains 'a', 'b', and 'c'. Instead of counting all valid substrings directly, we can iterate through the string and for each character, count how many valid substrings *end* at that position. A valid substring ending at index `i` must start at some index `j <= i` such that the window `[j, i]` contains all three characters.

## Approach

### 1. Brute Force
- **Idea**: Generate all possible substrings and check if each contains 'a', 'b', and 'c'.
- **Optimization**: For each starting position `i`, expand `j` until you find all three characters. Once you find a valid substring `[i, j]`, every substring starting at `i` and ending at any index `k >= j` is also valid.
- **Time Complexity**: $O(N^2)$ (or $O(N)$ with the optimization in the best case, but still $O(N^2)$ worst case).
- **Space Complexity**: $O(1)$

### 2. Optimal Approach (Last Seen Index)
- **Idea**: For every index `i` in the string, if we know the last seen positions of 'a', 'b', and 'c' before or at `i`, the smallest of these three positions tells us the starting point of the shortest valid substring ending at `i`.
- **Steps**:
    1. Initialize `last_seen = {'a': -1, 'b': -1, 'c': -1}` and `count = 0`.
    2. Iterate through the string with index `i`.
    3. Update `last_seen[s[i]] = i`.
    4. If all characters have been seen (i.e., no -1 in `last_seen`):
        - The number of valid substrings ending at `i` is `min(last_seen['a'], last_seen['b'], last_seen['c']) + 1`.
        - Add this value to `count`.
    5. Return `count`.
- **Why it works**: If the last 'a' is at index 2, last 'b' at 5, and last 'c' at 4, then the substring from index 2 to 5 is the smallest valid substring ending at 5. Any substring starting at index 0, 1, or 2 and ending at 5 will also be valid. Thus, there are `min(2, 5, 4) + 1 = 3` such substrings.

## Implementation

```python
def numberOfSubstrings(s: str) -> int:
    last_seen = {'a': -1, 'b': -1, 'c': -1}
    count = 0
    
    for i, char in enumerate(s):
        last_seen[char] = i
        if -1 not in last_seen.values():
            # Smallest of last seen indices determines the count
            count += min(last_seen.values()) + 1
            
    return count
```

## Complexity
- **Time Complexity**: $O(N)$ as we traverse the string once.
- **Space Complexity**: $O(1)$ to store the last seen indices of three characters.

## Pattern
- **Sliding Window / Two Pointers (Counting Substrings)**: This variation focuses on counting valid substrings by looking at their ending positions and identifying the furthest possible starting point.

## Common Mistakes
- **Off-by-one error**: Adding `min_index` instead of `min_index + 1`.
- **Initialization**: Not handling the case where some characters haven't been seen yet (e.g., using -1 and not checking).

## Related Problems
- Longest Substring Without Repeating Characters
- Subarrays with K Different Integers
- Count Number of Nice Subarrays

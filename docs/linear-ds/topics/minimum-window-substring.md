# Minimum Window Substring

## Problem Summary
Given two strings `s` and `t` of lengths `m` and `n` respectively, return the minimum window substring of `s` such that every character in `t` (including duplicates) is included in the window. If there is no such substring, return the empty string `""`.

## Intuition
The goal is to find the smallest range `[L, R]` in `s` that contains all characters of `t`. We can use a sliding window:
1.  Expand the window to the right until it contains all characters of `t`.
2.  Once valid, shrink the window from the left to find the smallest possible valid window.
3.  Repeat until the right pointer reaches the end.

A frequency map (or hash array) helps keep track of characters we need from `t`. A `count` variable can track how many required characters are currently satisfied.

## Approach

### 1. Brute Force
- Generate all possible substrings of `s`.
- For each substring, check if it contains all characters of `t` with the required frequencies.
- Keep track of the minimum length substring that satisfies the condition.
- **Time Complexity:** $O(N^2)$ to generate substrings, and $O(N)$ or $O(256)$ to check each. Overall $O(N^2)$.
- **Space Complexity:** $O(256)$ for character counting.

### 2. Optimal (Sliding Window)
- **Initialization:**
    - Create a frequency map `hash` for string `t`.
    - `L = 0, R = 0, minLen = INT_MAX, startIdx = -1, count = 0`.
    - `count` stores how many characters of `t` are currently "satisfied" in the window.
- **Execution:**
    - Move `R` from `0` to `n-1`:
        - If `hash[s[R]] > 0`, it means `s[R]` is a required character still needed, so `count++`.
        - Decrement `hash[s[R]]`. (Note: It can go below 0).
        - While `count == t.length()`:
            - If `R - L + 1 < minLen`, update `minLen` and `startIdx`.
            - To shrink, increment `hash[s[L]]`.
            - If `hash[s[L]] > 0` after incrementing, it means an essential character is now missing from the window, so `count--`.
            - Increment `L`.
- **Result:** Return the substring of `s` starting at `startIdx` with length `minLen`. If `startIdx` is still `-1`, return `""`.

## Complexity
- **Time Complexity:** $O(N + M)$, where $N$ is the length of `s` and $M$ is the length of `t`. Each pointer moves at most $N$ times.
- **Space Complexity:** $O(256)$ or $O(1)$ as we use a fixed-size array/map for character frequencies.

## Pattern
- **Sliding Window (Variable Size):** Expanding to find a valid window and shrinking to optimize (minimize).

## Common Mistakes
- **Handling Duplicates:** Ensure the frequency map accounts for multiple occurrences of the same character in `t`.
- **Count Logic:** Only increment `count` when the character's frequency in the map is *strictly positive* before decrementing.
- **Negative Frequencies:** Characters in `s` that are not in `t` or are extra occurrences will have negative frequencies in the map during the process. This is normal.

## Related Problems
- [Longest Substring with At Most K Distinct Characters](./longest-substring-with-at-most-k-distinct-characters.md)
- [Minimum Window Substring](./minimum-window-substring.md)

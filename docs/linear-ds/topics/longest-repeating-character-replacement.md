# Longest Repeating Character Replacement

## Problem Summary
Given a string `s` consisting of uppercase English letters and an integer `k`, you can choose to change at most `k` characters of the string to any other uppercase English character. Your goal is to find the length of the longest substring containing the same letter after performing at most `k` replacements.

## Intuition
The core idea is that for any substring, if we want to turn it into a string of repeating characters with the minimum number of replacements, we should keep the character that appears most frequently and replace all others.
- **Number of replacements needed** = `Length of substring - Frequency of the most frequent character`.
- A substring is valid if: `(Length - Max Frequency) <= k`.

## Approach

### 1. Brute Force
- Generate all possible substrings using two nested loops.
- For each substring, count the frequency of each character (A-Z).
- Find the character with the maximum frequency.
- Check if `(Length - Max Frequency) <= k`.
- Update the global maximum length if the condition is met.
- **Time Complexity:** $O(N^2)$ (or $O(N^2 \times 26)$ depending on frequency counting).
- **Space Complexity:** $O(26)$ to store character counts.

### 2. Better Approach (Sliding Window)
- Use two pointers, `L` and `R`, to represent a sliding window.
- Expand the window by moving `R`. Update the frequency map and `maxFrequency`.
- While the window is invalid (`(R - L + 1) - maxFrequency > k`):
    - Decrement the frequency of `s[L]`.
    - Increment `L`.
    - (Optional but common in this stage) Re-scan the frequency map to update `maxFrequency`.
- Update `maxLen = max(maxLen, R - L + 1)` at each step.
- **Time Complexity:** $O(N \times 26)$ or $O(2N)$ if `maxFrequency` isn't re-scanned (but technically we need the true max frequency of the current window here).
- **Space Complexity:** $O(26)$.

### 3. Optimal Approach (Optimized Sliding Window)
- Similar to the sliding window above, but we realize we don't need to decrease `maxFrequency` when shrinking the window.
- We are only interested in finding a window larger than our current `maxLen`. A larger window would require an even larger `maxFrequency` to be valid.
- Instead of a `while` loop, we use an `if` statement. If the window becomes invalid, we just shift it by incrementing `L` once, effectively keeping the window size the same until we find a character that increases `maxFrequency`.
- **Time Complexity:** $O(N)$.
- **Space Complexity:** $O(26)$.

## Complexity
- **Time Complexity:** $O(N)$ where $N$ is the length of the string. We traverse the string once with the right pointer.
- **Space Complexity:** $O(26)$ or $O(1)$ since the hash map size is constant (number of uppercase English letters).

## Pattern
- **Sliding Window (Variable/Optimized):** Using a window and maintaining a frequency count to satisfy a constraint.

## Common Mistakes
- **Re-calculating Max Frequency:** In the optimal approach, it's not necessary to re-scan the map for a new `maxFrequency` when moving `L`.
- **Off-by-one errors:** Carefully manage `R - L + 1` for length calculations.
- **Character set:** Ensure the frequency array is sized correctly (26 for uppercase letters).

## Related Problems
- [Max Consecutive Ones III](./max-consecutive-ones-iii.md)
- [Longest Substring with At Most K Distinct Characters](./longest-substring-with-at-most-k-distinct-characters.md)
- [Binary Subarrays With Sum](./binary-subarrays-with-sum.md)

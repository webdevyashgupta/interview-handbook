# Sliding Window

**Topic 3 of 18 | NeetCode 150 Interview Handbook**  
[← Back to Index](./index.md)

---

## Problems in this Section

| # | Problem | Difficulty |
|---|---------|------------|
| 15 | [Best Time to Buy and Sell Stock](#15-best-time-to-buy-and-sell-stock) | 🟢 Easy |
| 16 | [Longest Substring Without Repeating Characters](#16-longest-substring-without-repeating-characters) | 🟡 Medium |
| 17 | [Longest Repeating Character Replacement](#17-longest-repeating-character-replacement) | 🟡 Medium |
| 18 | [Permutation in String](#18-permutation-in-string) | 🟡 Medium |
| 19 | [Minimum Window Substring](#19-minimum-window-substring) | 🔴 Hard |
| 20 | [Sliding Window Maximum](#20-sliding-window-maximum) | 🔴 Hard |

---

## 15. Best Time to Buy and Sell Stock

**LeetCode #121 | Difficulty: 🟢 Easy**

### Problem Statement

Given an array `prices` where `prices[i]` is the price of a stock on day `i`, return the maximum profit you can achieve from one buy and one sell. You must buy before you sell. If no profit is possible, return `0`.

```
Input:  prices = [7,1,5,3,6,4]
Output: 5  (buy day 2 at 1, sell day 5 at 6)

Input:  prices = [7,6,4,3,1]
Output: 0  (no profitable transaction)
```

### Intuition

Track the **minimum price seen so far** as you scan left to right. At each day, compute the profit if you sold today (`price - min_price`). Track the maximum of these profits.

This is a sliding window in spirit — the "window" is always from the minimum price day to today.

### Solution

```python
def maxProfit(prices: list[int]) -> int:
    min_price = float('inf')
    max_profit = 0

    for price in prices:
        min_price = min(min_price, price)
        max_profit = max(max_profit, price - min_price)

    return max_profit
```

**Time:** O(n) | **Space:** O(1)

### Interview Traps

**Trap 1 — Buying after selling**
You must buy before you sell. Tracking `min_price` as a running minimum ensures the buy day always precedes the sell day.

**Trap 2 — Returning negative profit**
If prices only decrease, `price - min_price` could be 0 but never negative (min_price updates before the profit check). Initialize `max_profit = 0` to handle the "no trade" case.

**Trap 3 — "What if you can make multiple transactions?"**
That's LeetCode #122 (Buy and Sell Stock II). The strategy changes entirely — add up all positive day-to-day differences.
```python
# Multiple transactions
return sum(max(0, prices[i] - prices[i-1]) for i in range(1, len(prices)))
```

**Trap 4 — "What if you can hold at most k transactions?"**
That's LeetCode #188 — requires DP. Know this exists as an extension.

**Trap 5 — Using two nested loops (O(n²))**
✅ The single-pass minimum-tracking approach is O(n) and is the expected answer.

---

## 16. Longest Substring Without Repeating Characters

**LeetCode #3 | Difficulty: 🟡 Medium**

### Problem Statement

Given a string `s`, find the length of the longest substring without repeating characters.

```
Input:  s = "abcabcbb"
Output: 3  ("abc")

Input:  s = "bbbbb"
Output: 1  ("b")

Input:  s = "pwwkew"
Output: 3  ("wke")
```

### Intuition

Use a **sliding window** with a HashMap tracking the last seen index of each character. The left pointer jumps forward whenever we encounter a repeated character — past the previous occurrence of that character. The window `[left, right]` always contains unique characters.

### Solution

```python
def lengthOfLongestSubstring(s: str) -> int:
    last_seen = {}
    left = 0
    best = 0

    for right, char in enumerate(s):
        if char in last_seen and last_seen[char] >= left:
            left = last_seen[char] + 1
        last_seen[char] = right
        best = max(best, right - left + 1)

    return best
```

**Time:** O(n) | **Space:** O(min(n, 26)) — at most 26 chars for ASCII

### Interview Traps

**Trap 1 — Moving left pointer incorrectly**
```python
# ❌ Bug: doesn't guard against left moving backward
if char in last_seen:
    left = last_seen[char] + 1  # last_seen[char] could be < left!

# ✅ Correct: only advance left, never retreat
if char in last_seen and last_seen[char] >= left:
    left = last_seen[char] + 1
```

**Trap 2 — Using a set instead of a map**
A set works but requires shrinking the window one step at a time (O(n²) worst case with many duplicates). A HashMap enables O(1) left-pointer jumps.

**Trap 3 — Off-by-one in window length**
Window `[left, right]` has length `right - left + 1`, not `right - left`.

**Trap 4 — Empty string**
`s = ""` → the loop never executes → returns `best = 0`. Correct.

**Trap 5 — Unicode characters**
`last_seen` as a `dict` handles any character set. A fixed array `[0]*26` breaks with non-ASCII input.

---

## 17. Longest Repeating Character Replacement

**LeetCode #424 | Difficulty: 🟡 Medium**

### Problem Statement

Given a string `s` and an integer `k`, you can replace any `k` characters in the string with any other character. Return the length of the longest substring containing the same letter you can get after performing at most `k` replacements.

```
Input:  s = "ABAB", k = 2
Output: 4

Input:  s = "AABABBA", k = 1
Output: 4
```

### Intuition

Use a sliding window. Within a window of length `L`, the minimum replacements needed = `L - max_count` (where `max_count` is the frequency of the most common character).

If `L - max_count > k`, the window is invalid — shrink from the left.

Key insight: we never need to shrink `max_count` — we only care if the window can grow. This makes the window length monotonically non-decreasing.

### Solution

```python
from collections import defaultdict

def characterReplacement(s: str, k: int) -> int:
    count = defaultdict(int)
    left = 0
    max_count = 0
    result = 0

    for right in range(len(s)):
        count[s[right]] += 1
        max_count = max(max_count, count[s[right]])

        # Window size - most frequent char count > k → invalid
        if (right - left + 1) - max_count > k:
            count[s[left]] -= 1
            left += 1

        result = max(result, right - left + 1)

    return result
```

**Time:** O(n) | **Space:** O(1) — at most 26 keys

### Interview Traps

**Trap 1 — Recomputing max_count after shrinking**
When we shrink the window, we don't recompute `max_count`. This seems wrong but is correct — `max_count` only needs to increase to improve our answer. An "invalid" max_count only means the window won't grow, not that we'll return a wrong answer.

**Trap 2 — Misunderstanding the shrink condition**
`(right - left + 1) - max_count > k` means: even if we replace all non-dominant chars, we still exceed `k`.  
✅ Rearranged: `window_size - max_frequency > k`.

**Trap 3 — Using `max(count.values())` on every iteration**
This is O(26) per step — acceptable but unnecessary. `max_count` can be maintained incrementally.

**Trap 4 — Lowercase vs uppercase**
The problem uses uppercase. Ensure your frequency array or dict is consistent with the input character set.

**Trap 5 — k = 0 edge case**
With `k = 0`, the answer is the longest run of a single character. The algorithm handles this correctly — the window shrinks whenever any non-dominant character is encountered.

---

## 18. Permutation in String

**LeetCode #567 | Difficulty: 🟡 Medium**

### Problem Statement

Given strings `s1` and `s2`, return `true` if `s2` contains a permutation of `s1` (i.e., a substring of `s2` that is an anagram of `s1`).

```
Input:  s1 = "ab", s2 = "eidbaooo"
Output: true  ("ba" is a permutation of "ab")

Input:  s1 = "ab", s2 = "eidboaoo"
Output: false
```

### Intuition

Use a **fixed-size sliding window** of length `len(s1)` over `s2`. Maintain a character frequency comparison between the window and `s1`. Track how many of the 26 characters currently have matching frequencies — when all 26 match, a permutation is found.

### Solution

```python
def checkInclusion(s1: str, s2: str) -> bool:
    if len(s1) > len(s2):
        return False

    count1 = [0] * 26
    count2 = [0] * 26

    for c in s1:
        count1[ord(c) - ord('a')] += 1

    matches = 0  # number of chars with equal frequency in both windows
    for i in range(26):
        if count1[i] == count2[i]:
            matches += 1

    for i in range(len(s2)):
        idx = ord(s2[i]) - ord('a')
        count2[idx] += 1

        # Check if this char now matches
        if count2[idx] == count1[idx]:
            matches += 1
        elif count2[idx] == count1[idx] + 1:
            matches -= 1  # was matching, now over

        # Slide window: remove leftmost char if window too big
        if i >= len(s1):
            left_idx = ord(s2[i - len(s1)]) - ord('a')
            count2[left_idx] -= 1
            if count2[left_idx] == count1[left_idx]:
                matches += 1
            elif count2[left_idx] == count1[left_idx] - 1:
                matches -= 1

        if matches == 26:
            return True

    return False
```

**Time:** O(n) | **Space:** O(1) — fixed 26-element arrays

**Simpler but slightly slower alternative:**
```python
from collections import Counter

def checkInclusion(s1: str, s2: str) -> bool:
    target = Counter(s1)
    n = len(s1)
    window = Counter(s2[:n])
    if window == target:
        return True
    for i in range(n, len(s2)):
        window[s2[i]] += 1
        window[s2[i - n]] -= 1
        if window[s2[i - n]] == 0:
            del window[s2[i - n]]
        if window == target:
            return True
    return False
```

### Interview Traps

**Trap 1 — Comparing full Counter objects every step**
`Counter == Counter` is O(26) each time — technically O(1) but adds overhead. The `matches` variable approach is cleaner and immediately returns `True`.

**Trap 2 — Variable-size window instead of fixed-size**
The window must always be exactly `len(s1)` — it's a fixed-size sliding window.  
✅ Add the shrink step when `i >= len(s1)`.

**Trap 3 — Not handling `len(s1) > len(s2)`**
If `s1` is longer than `s2`, a permutation is impossible.  
✅ Early return `False`.

**Trap 4 — Confusing with Group Anagrams or Valid Anagram**
This problem uses a sliding window — you're checking a *moving* window, not the entire string.

**Trap 5 — Forgetting to delete zero-count keys in Counter approach**
If you don't delete zero entries, `Counter({'a': 0}) != Counter()` and comparison fails.  
✅ Either delete zero-count entries or use the array-based `matches` approach.

---

## 19. Minimum Window Substring

**LeetCode #76 | Difficulty: 🔴 Hard**

### Problem Statement

Given strings `s` and `t`, return the minimum window substring of `s` such that every character in `t` (including duplicates) is included. If no such window exists, return `""`.

```
Input:  s = "ADOBECODEBANC", t = "ABC"
Output: "BANC"

Input:  s = "a", t = "a"
Output: "a"
```

### Intuition

Use a variable-size sliding window. Expand the right pointer to include characters from `t`. Once all characters are satisfied, try to shrink from the left to find the minimum valid window. Track how many distinct characters from `t` are currently satisfied (`have == need`).

### Solution

```python
from collections import Counter

def minWindow(s: str, t: str) -> str:
    if not t or not s:
        return ""

    t_count = Counter(t)
    need = len(t_count)   # number of unique chars in t we need to satisfy
    have = 0              # number of unique chars currently satisfied

    window = {}
    result = ""
    result_len = float('inf')
    left = 0

    for right in range(len(s)):
        char = s[right]
        window[char] = window.get(char, 0) + 1

        if char in t_count and window[char] == t_count[char]:
            have += 1

        while have == need:
            # Update result
            if (right - left + 1) < result_len:
                result_len = right - left + 1
                result = s[left:right + 1]

            # Shrink from left
            left_char = s[left]
            window[left_char] -= 1
            if left_char in t_count and window[left_char] < t_count[left_char]:
                have -= 1
            left += 1

    return result
```

**Time:** O(|s| + |t|) | **Space:** O(|s| + |t|)

### Interview Traps

**Trap 1 — Tracking character counts incorrectly**
`have` increments only when `window[char] == t_count[char]` — not just when `char in t`. Over-counting a character doesn't increase `have`.

**Trap 2 — Forgetting to shrink the window**
Once `have == need`, shrink the left as far as possible before moving right again. Missing the inner `while` makes this O(n²) or returns a non-minimum window.

**Trap 3 — Not handling `t` with duplicate characters**
`t = "AAB"` means you need two A's and one B. `t_count` handles this automatically — `have` only increments when the window's count of a char meets `t_count`'s requirement.

**Trap 4 — Returning a wrong result when no window exists**
If `result` never updates, return `""`. Initialize `result = ""` and only update when a valid window is found.

**Trap 5 — "Why use `have/need` instead of just comparing counters?"**
Comparing full counters on every step is O(|t|) per character. `have/need` reduces the validity check to O(1) per step.

---

## 20. Sliding Window Maximum

**LeetCode #239 | Difficulty: 🔴 Hard**

### Problem Statement

Given an integer array `nums` and a sliding window of size `k`, return the maximum value in each window position as the window moves from left to right.

```
Input:  nums = [1,3,-1,-3,5,3,6,7], k = 3
Output: [3,3,5,5,6,7]
```

### Intuition

Use a **monotonic deque** (double-ended queue) that stores indices in decreasing order of their values. The front of the deque is always the index of the current window's maximum.

- **Add right:** Pop from the back while `nums[deque.back()] <= nums[right]` (smaller elements can never be the max while the current element is in the window).
- **Remove expired:** Pop from the front if `deque.front() < left` (out of window).
- **Query max:** `nums[deque.front()]`.

### Solution

```python
from collections import deque

def maxSlidingWindow(nums: list[int], k: int) -> list[int]:
    dq = deque()  # stores indices, decreasing by value
    result = []
    left = 0

    for right in range(len(nums)):
        # Remove smaller elements from back
        while dq and nums[dq[-1]] <= nums[right]:
            dq.pop()
        dq.append(right)

        # Remove out-of-window index from front
        if dq[0] < left:
            dq.popleft()

        # Window is fully formed
        if right >= k - 1:
            result.append(nums[dq[0]])
            left += 1

    return result
```

**Time:** O(n) — each element added and removed at most once | **Space:** O(k)

### Interview Traps

**Trap 1 — Using a max-heap**
A heap gives O(n log k) — correct but not optimal. The monotonic deque achieves O(n).  
✅ Know the heap approach as a fallback, but lead with the deque.

**Trap 2 — Storing values instead of indices in the deque**
You need indices to check if the front element has expired (left out of the window).  
✅ Always store indices; retrieve values via `nums[dq[0]]`.

**Trap 3 — Wrong eviction condition**
```python
# ❌ Wrong: removes front when it equals left, not when it's strictly less
if dq[0] <= left: dq.popleft()

# ✅ Correct: remove when index is out of window
if dq[0] < left: dq.popleft()
```

**Trap 4 — Not waiting for the window to fully form**
Only add to `result` once `right >= k - 1`.

**Trap 5 — "Explain why the deque is monotonically decreasing"**
If a smaller element is in the deque behind a larger one, it will never be the window maximum (the larger element is in the window at the same time and will outlast it or expire later). It's safe to discard smaller elements immediately.

---

*[← Back to Index](./index.md) | [Next: Stack →](./04_stack.md)*

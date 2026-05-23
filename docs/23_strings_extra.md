# Strings — Extra Problems

**Grind 169 Supplement | Not in NeetCode 150**  
[← Back to Index](./index.md)

---

## Problems in this Section

| # | Problem | Difficulty |
|---|---------|------------|
| 1 | [Ransom Note](#1-ransom-note) | 🟢 Easy |
| 2 | [Longest Palindrome (LC #409)](#2-longest-palindrome) | 🟢 Easy |
| 3 | [Add Binary](#3-add-binary) | 🟢 Easy |
| 4 | [First Bad Version](#4-first-bad-version) | 🟢 Easy |
| 5 | [Find All Anagrams in a String](#5-find-all-anagrams-in-a-string) | 🟡 Medium |
| 6 | [String to Integer (atoi)](#6-string-to-integer-atoi) | 🟡 Medium |

---

## 1. Ransom Note

**LeetCode #383 | Difficulty: 🟢 Easy**

### Problem Statement

Given two strings `ransomNote` and `magazine`, return `true` if `ransomNote` can be constructed by using the letters from `magazine`. Each letter in `magazine` can only be used once.

```
Input:  ransomNote = "a", magazine = "b"
Output: false

Input:  ransomNote = "aa", magazine = "ab"
Output: false

Input:  ransomNote = "aa", magazine = "aab"
Output: true
```

### Intuition

Count character frequencies in `magazine`. Then for each character in `ransomNote`, check if the magazine has enough of that character. Decrement as you consume.

### Solution

```python
from collections import Counter

def canConstruct(ransomNote: str, magazine: str) -> bool:
    mag_count = Counter(magazine)

    for c in ransomNote:
        if mag_count[c] <= 0:
            return False
        mag_count[c] -= 1

    return True
```

**One-liner:**
```python
return not (Counter(ransomNote) - Counter(magazine))
```

**Time:** O(m + n) | **Space:** O(1) — at most 26 keys

### Interview Traps

**Trap 1 — Checking if ransomNote is a subset of magazine (set-based)**
Sets lose frequency info — `"aa"` from `"ab"` would incorrectly return True.  
✅ Always use frequency counts, not sets.

**Trap 2 — Counter subtraction semantics**
`Counter(a) - Counter(b)` keeps only positive counts. If the result is empty, `a` can be formed from `b`.

**Trap 3 — "What if characters are unicode?"**
`Counter` handles any characters. A fixed `[0]*26` array breaks.

---

## 2. Longest Palindrome

**LeetCode #409 | Difficulty: 🟢 Easy**

### Problem Statement

Given a string `s`, return the length of the longest palindrome that can be built with those characters (rearranging is allowed).

```
Input:  s = "abccccdd"
Output: 7  ("dccaccd" or similar)

Input:  s = "a"
Output: 1
```

### Intuition

A palindrome uses pairs of characters symmetrically. Count all characters. Every pair contributes 2 to the length. If any character has an odd count, one can go in the center (+1).

### Solution

```python
from collections import Counter

def longestPalindrome(s: str) -> int:
    counts = Counter(s)
    length = 0
    has_odd = False

    for count in counts.values():
        length += (count // 2) * 2
        if count % 2 == 1:
            has_odd = True

    return length + (1 if has_odd else 0)
```

**Time:** O(n) | **Space:** O(1)

### Interview Traps

**Trap 1 — Confusing with Longest Palindromic Substring (LC #5)**
LC #5: find the longest palindromic *substring* in a given string (can't rearrange).  
LC #409: build the longest palindrome by *rearranging* characters — completely different.

**Trap 2 — Forgetting the center character**
If any character has an odd count, place one in the center. Add `+1` only once, not once per odd-count character.

**Trap 3 — All even counts**
No center needed → `has_odd` stays False → no `+1`. Handled correctly.

---

## 3. Add Binary

**LeetCode #67 | Difficulty: 🟢 Easy**

### Problem Statement

Given two binary strings `a` and `b`, return their sum as a binary string.

```
Input:  a = "11", b = "1"
Output: "100"

Input:  a = "1010", b = "1011"
Output: "10101"
```

### Intuition

Simulate binary addition from right to left with a carry. At each position, sum the corresponding bits plus carry. The digit is `total % 2` and the new carry is `total // 2`. Collect digits in reverse and flip at the end.

### Solution

```python
def addBinary(a: str, b: str) -> str:
    i, j = len(a) - 1, len(b) - 1
    carry = 0
    result = []

    while i >= 0 or j >= 0 or carry:
        total = carry
        if i >= 0:
            total += int(a[i])
            i -= 1
        if j >= 0:
            total += int(b[j])
            j -= 1
        result.append(str(total % 2))
        carry = total // 2

    return ''.join(reversed(result))
```

**Time:** O(max(m, n)) | **Space:** O(max(m, n))

### Interview Traps

**Trap 1 — Converting to int and using bin()**
`bin(int(a, 2) + int(b, 2))[2:]` works in Python but defeats the purpose — implement the manual simulation.

**Trap 2 — Not handling the final carry**
`"1" + "1" = "10"` — the carry after the last column must be included. The `or carry` in the while condition handles this.

**Trap 3 — Appending in reverse**
Digits are generated LSB first. Reverse at the end with `''.join(reversed(result))`.

**Trap 4 — Generalizing to other bases**
Same pattern works for any base — just change `% 2` and `// 2` to `% base` and `// base`.

---

## 4. First Bad Version

**LeetCode #278 | Difficulty: 🟢 Easy**

### Problem Statement

You have `n` versions `[1, 2, ..., n]` and an API `isBadVersion(version)`. Versions after the first bad one are also bad. Find the first bad version using the minimum number of API calls.

```
Input:  n = 5, bad = 4
Output: 4  (calls: isBadVersion(3)→F, isBadVersion(5)→T, isBadVersion(4)→T)
```

### Intuition

Classic **binary search on the answer**. Search space is `[1, n]`. If `isBadVersion(mid)` is True, the first bad version is at `mid` or earlier → `right = mid`. If False, it's after → `left = mid + 1`. Return `left` when converged.

### Solution

```python
def firstBadVersion(n: int) -> int:
    left, right = 1, n

    while left < right:
        mid = left + (right - left) // 2
        if isBadVersion(mid):
            right = mid        # could be first bad, keep it
        else:
            left = mid + 1     # definitely not bad

    return left
```

**Time:** O(log n) | **Space:** O(1)

### Interview Traps

**Trap 1 — Linear scan O(n)**
Calling `isBadVersion` for every version uses O(n) API calls. Binary search uses O(log n).

**Trap 2 — Using `left <= right` with `right = mid - 1`**
That misses the first bad version if it's exactly at `mid`. Use `left < right` and `right = mid`.

**Trap 3 — Integer overflow in mid**
`mid = left + (right - left) // 2` — safe in all languages.

**Trap 4 — "This is binary search on answer space, not array"**
There's no array — you're binary searching over the range `[1, n]` itself. This is the same pattern as Koko Eating Bananas.

---

## 5. Find All Anagrams in a String

**LeetCode #438 | Difficulty: 🟡 Medium**

### Problem Statement

Given strings `s` and `p`, return all start indices of `p`'s anagrams in `s`.

```
Input:  s = "cbaebabacd", p = "abc"
Output: [0,6]

Input:  s = "abab", p = "ab"
Output: [0,1,2]
```

### Intuition

Fixed-size **sliding window** of length `len(p)`. Maintain character frequency arrays for the window and `p`. Track how many characters currently have matching frequencies (`matches`). Slide the window one step at a time, updating `matches` as characters enter and leave.

### Solution

```python
def findAnagrams(s: str, p: str) -> list[int]:
    if len(p) > len(s):
        return []

    p_count = [0] * 26
    s_count = [0] * 26

    for c in p:
        p_count[ord(c) - ord('a')] += 1

    result = []
    matches = 0
    for i in range(26):
        if p_count[i] == s_count[i]:
            matches += 1

    for i in range(len(s)):
        # Add right character
        idx = ord(s[i]) - ord('a')
        s_count[idx] += 1
        if s_count[idx] == p_count[idx]:
            matches += 1
        elif s_count[idx] == p_count[idx] + 1:
            matches -= 1  # was matching, now over

        # Remove left character when window is full
        if i >= len(p):
            left_idx = ord(s[i - len(p)]) - ord('a')
            s_count[left_idx] -= 1
            if s_count[left_idx] == p_count[left_idx]:
                matches += 1
            elif s_count[left_idx] == p_count[left_idx] - 1:
                matches -= 1

        if matches == 26:
            result.append(i - len(p) + 1)

    return result
```

**Time:** O(n) | **Space:** O(1)

### Interview Traps

**Trap 1 — Comparing full Counter objects each step**
O(26) per step — technically O(1) but the `matches` variable approach is cleaner.

**Trap 2 — Variable-size window**
The window must be exactly `len(p)`. Start removing the left character once `i >= len(p)`.

**Trap 3 — Confusing with Permutation in String (LC #567)**
LC #567: return True/False. LC #438: return all start indices. Same algorithm, different output.

**Trap 4 — Off-by-one in start index**
When `i` is the last character of the window, the start index is `i - len(p) + 1`.

---

## 6. String to Integer (atoi)

**LeetCode #8 | Difficulty: 🟡 Medium**

### Problem Statement

Implement `atoi` which converts a string to a 32-bit signed integer. Handle leading whitespace, optional sign, valid digits, invalid characters (stop parsing), and overflow (clamp to `[-2³¹, 2³¹-1]`).

```
Input:  s = "42"
Output: 42

Input:  s = "   -42"
Output: -42

Input:  s = "4193 with words"
Output: 4193

Input:  s = "-91283472332"
Output: -2147483648  (clamped to INT_MIN)
```

### Intuition

Process the string step by step:
1. Strip leading whitespace.
2. Detect optional `+` or `-` sign.
3. Read digits one by one, building the number.
4. Stop at the first non-digit character.
5. Clamp to 32-bit signed integer range.

### Solution

```python
def myAtoi(s: str) -> int:
    INT_MIN, INT_MAX = -2**31, 2**31 - 1
    i = 0
    n = len(s)

    # Step 1: Skip whitespace
    while i < n and s[i] == ' ':
        i += 1

    # Step 2: Read sign
    sign = 1
    if i < n and s[i] in '+-':
        if s[i] == '-':
            sign = -1
        i += 1

    # Step 3: Read digits
    result = 0
    while i < n and s[i].isdigit():
        digit = int(s[i])
        # Check overflow before updating
        if result > (INT_MAX - digit) // 10:
            return INT_MAX if sign == 1 else INT_MIN
        result = result * 10 + digit
        i += 1

    return sign * result
```

**Time:** O(n) | **Space:** O(1)

### Interview Traps

**Trap 1 — Using Python's `int()` directly**
`int("  -42abc")` raises an error. The problem requires manual parsing with graceful stopping.

**Trap 2 — Not checking overflow before multiplying**
After reading each digit, check if `result * 10 + digit` would overflow before actually computing it.

**Trap 3 — Multiple signs**
`"+-12"` → sign is `+`, next char is `-` which is not a digit → stop. Return 0. Only one sign is valid.

**Trap 4 — Empty string or all whitespace**
Loop exits without reading any digits → `result = 0` → return 0. Correct.

**Trap 5 — "What about leading zeros?"**
`"007"` → 7. The digit reading loop naturally handles leading zeros (they just contribute 0 to the result).

---

*[← Back to Index](./index.md) | [Next: Graphs Extra →](./24_graphs_extra.md)*

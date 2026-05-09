# Arrays & Hashing

**Topic 1 of 18 | NeetCode 150 Interview Handbook**  
[← Back to Index](./index.md)

---

## Problems in this Section

| # | Problem | Difficulty |
|---|---------|------------|
| 1 | [Contains Duplicate](#1-contains-duplicate) | 🟢 Easy |
| 2 | [Valid Anagram](#2-valid-anagram) | 🟢 Easy |
| 3 | [Two Sum](#3-two-sum) | 🟢 Easy |
| 4 | [Group Anagrams](#4-group-anagrams) | 🟡 Medium |
| 5 | [Top K Frequent Elements](#5-top-k-frequent-elements) | 🟡 Medium |
| 6 | [Product of Array Except Self](#6-product-of-array-except-self) | 🟡 Medium |
| 7 | [Valid Sudoku](#7-valid-sudoku) | 🟡 Medium |
| 8 | [Encode and Decode Strings](#8-encode-and-decode-strings) | 🟡 Medium |
| 9 | [Longest Consecutive Sequence](#9-longest-consecutive-sequence) | 🟡 Medium |

---

## 1. Contains Duplicate

**LeetCode #217 | Difficulty: 🟢 Easy**

### Problem Statement

Given an integer array `nums`, return `true` if any value appears at least twice, and `false` if every element is distinct.

```
Input:  nums = [1, 2, 3, 1]
Output: true

Input:  nums = [1, 2, 3, 4]
Output: false
```

### Intuition

The question boils down to: *"Have I seen this number before?"* A **HashSet** answers this in O(1). Iterate once, check membership before inserting — return `true` the moment a duplicate is found.

### Solution

```python
def containsDuplicate(nums: list[int]) -> bool:
    seen = set()
    for num in nums:
        if num in seen:
            return True
        seen.add(num)
    return False
```

**Time:** O(n) | **Space:** O(n)

**Pythonic one-liner:**
```python
return len(nums) != len(set(nums))
```
> ⚠️ One-liner builds the full set before checking — no early exit. Prefer the loop for large inputs with early duplicates.

**Sorting alternative (O(1) space):**
```python
nums.sort()
for i in range(1, len(nums)):
    if nums[i] == nums[i - 1]:
        return True
return False
```
**Time:** O(n log n) | **Space:** O(1) — but modifies input.

### Interview Traps

**Trap 1 — Proposing sort without justifying the trade-off**  
Sorting works but is O(n log n). If you propose it, the interviewer will ask for better.  
✅ Lead with HashSet. Offer sorting only if the interviewer asks for O(1) extra space.

**Trap 2 — Missing early-exit**  
Checking *after* inserting wastes time and can produce wrong results.  
✅ Always check membership *before* adding to the set.

**Trap 3 — "What if the array is too large for memory?"**  
✅ Discuss external sorting, chunked processing, or a probabilistic structure like a **Bloom filter**.

**Trap 4 — Modifying the original array**  
Sorting is destructive. Always ask: *"Can I modify the input?"*

---

## 2. Valid Anagram

**LeetCode #242 | Difficulty: 🟢 Easy**

### Problem Statement

Given two strings `s` and `t`, return `true` if `t` is an anagram of `s`.

```
Input:  s = "anagram", t = "nagaram"
Output: true

Input:  s = "rat", t = "car"
Output: false
```

### Intuition

Two strings are anagrams if and only if they contain the **exact same character frequencies**. Build a frequency map from `s`, decrement using `t`, and check nothing went negative.

### Solution

```python
from collections import Counter

def isAnagram(s: str, t: str) -> bool:
    if len(s) != len(t):
        return False
    return Counter(s) == Counter(t)
```

**Manual version:**
```python
def isAnagram(s: str, t: str) -> bool:
    if len(s) != len(t):
        return False
    count = {}
    for c in s:
        count[c] = count.get(c, 0) + 1
    for c in t:
        count[c] = count.get(c, 0) - 1
        if count[c] < 0:
            return False
    return True
```

**Time:** O(n) | **Space:** O(1) — at most 26 keys for lowercase English

**Sorting alternative:**
```python
return sorted(s) == sorted(t)  # O(n log n)
```

### Interview Traps

**Trap 1 — Skipping the length check**  
Without it, you waste time building maps for clearly incompatible strings.  
✅ Always add `if len(s) != len(t): return False` as the first line.

**Trap 2 — Unicode follow-up**  
*"What if strings contain Unicode characters?"*  
✅ `dict` / `Counter` generalizes to any character — unlike a fixed `int[26]` array.
```python
# ❌ Breaks with Unicode
count[ord(char) - ord('a')] += 1  # IndexError for non-ASCII

# ✅ Works universally
count = Counter(s)
```

**Trap 3 — Case sensitivity**  
*"What if there are uppercase letters?"*  
✅ Clarify upfront. If case-insensitive: `Counter(s.lower()) == Counter(t.lower())`.

**Trap 4 — Using sorted() as the final answer**  
✅ Mention both, lead with the O(n) Counter approach, and explain the trade-off.

---

## 3. Two Sum

**LeetCode #1 | Difficulty: 🟢 Easy**

### Problem Statement

Given an array of integers `nums` and integer `target`, return indices of the two numbers that add up to `target`. Exactly one solution exists; you may not use the same element twice.

```
Input:  nums = [2, 7, 11, 15], target = 9
Output: [0, 1]

Input:  nums = [3, 2, 4], target = 6
Output: [1, 2]
```

### Intuition

For every number `x`, its **complement** is `target - x`. Use a HashMap (value → index) to check in O(1) if the complement has already been seen. One pass, constant-time lookup.

### Solution

```python
def twoSum(nums: list[int], target: int) -> list[int]:
    seen = {}  # value -> index
    for i, num in enumerate(nums):
        complement = target - num
        if complement in seen:
            return [seen[complement], i]
        seen[num] = i
    return []
```

**Time:** O(n) | **Space:** O(n)

### Interview Traps

**Trap 1 — Starting and staying at brute force**
```python
# ❌ O(n²) — never submit as final answer
for i in range(len(nums)):
    for j in range(i + 1, len(nums)):
        if nums[i] + nums[j] == target:
            return [i, j]
```
✅ Acknowledge brute force, then immediately pivot to the HashMap O(n) solution.

**Trap 2 — Checking complement after inserting (uses same element twice)**
```python
# ❌ Bug: nums=[3,3], target=6 → seen[3]=0 inserted first → finds itself
seen[num] = i
if complement in seen: ...

# ✅ Check before insert
if complement in seen: return [seen[complement], i]
seen[num] = i
```

**Trap 3 — Sorted array follow-up**  
*"What if the array is sorted? Can you use O(1) space?"*
```python
# Two-pointer approach for sorted array
left, right = 0, len(nums) - 1
while left < right:
    s = nums[left] + nums[right]
    if s == target: return [left, right]
    elif s < target: left += 1
    else: right -= 1
```

**Trap 4 — "Return all pairs if multiple solutions exist"**  
✅ Remove the early return; collect into a results list and continue iterating.

**Trap 5 — Duplicate values edge case**  
Walk through `nums = [3, 3], target = 6` to show the check-before-insert pattern handles it correctly.

---

## 4. Group Anagrams

**LeetCode #49 | Difficulty: 🟡 Medium**

### Problem Statement

Given an array of strings `strs`, group the anagrams together and return them in any order.

```
Input:  strs = ["eat","tea","tan","ate","nat","bat"]
Output: [["bat"],["nat","tan"],["ate","eat","tea"]]
```

### Intuition

Anagrams share the same **canonical form**. The two canonical choices:
1. **Sorted string** — `"eat"` → `"aet"`. Simple, O(k log k) per word.
2. **Character count tuple** — `"eat"` → `(1,0,0,...,1,...,1,...)`. O(k) per word, strictly better.

Use the canonical form as a HashMap key; all strings mapping to the same key are grouped together.

### Solution

**Approach 1: Sorted Key**
```python
from collections import defaultdict

def groupAnagrams(strs: list[str]) -> list[list[str]]:
    groups = defaultdict(list)
    for s in strs:
        key = tuple(sorted(s))
        groups[key].append(s)
    return list(groups.values())
```
**Time:** O(n · k log k) | **Space:** O(n · k)

**Approach 2: Character Count Key (Optimal)**
```python
from collections import defaultdict

def groupAnagrams(strs: list[str]) -> list[list[str]]:
    groups = defaultdict(list)
    for s in strs:
        count = [0] * 26
        for c in s:
            count[ord(c) - ord('a')] += 1
        groups[tuple(count)].append(s)
    return list(groups.values())
```
**Time:** O(n · k) | **Space:** O(n · k)

### Interview Traps

**Trap 1 — Not knowing what canonical key to use**  
✅ Think aloud: *"Anagrams are identical when sorted — that's my key."* Then upgrade: *"A character count tuple is O(k) vs O(k log k) for sorting."*

**Trap 2 — Using a mutable list as a dict key**
```python
# ❌ TypeError: unhashable type: 'list'
groups[[0]*26].append(s)

# ✅ Convert to tuple
groups[tuple([0]*26)].append(s)
```

**Trap 3 — Not using defaultdict**
```python
# ❌ Verbose
if key not in groups: groups[key] = []
groups[key].append(s)

# ✅ Clean
groups = defaultdict(list)
groups[key].append(s)
```

**Trap 4 — Unicode strings**  
✅ Use `Counter(s)` converted to `frozenset(Counter(s).items())` — generalizes beyond 26 letters.

**Trap 5 — Empty string edge case**  
`""` sorts to `""` and has an all-zero count tuple. Both approaches handle it correctly — verify this in your walkthrough.

---

## 5. Top K Frequent Elements

**LeetCode #347 | Difficulty: 🟡 Medium**

### Problem Statement

Given an integer array `nums` and integer `k`, return the `k` most frequent elements in any order.

```
Input:  nums = [1,1,1,2,2,3], k = 2
Output: [1, 2]
```

### Intuition

**Step 1:** Count frequencies with a HashMap.  
**Step 2:** Find top-k. Three options:
- Sort by frequency → O(n log n)
- Min-heap of size k → O(n log k)
- **Bucket sort** → O(n) ← optimal

Bucket sort insight: frequencies are bounded between 1 and n. Create n+1 buckets indexed by frequency, then read from highest bucket down until k elements are collected.

### Solution

**Approach 1: Heap — O(n log k)**
```python
import heapq
from collections import Counter

def topKFrequent(nums: list[int], k: int) -> list[int]:
    count = Counter(nums)
    return heapq.nlargest(k, count.keys(), key=lambda x: count[x])
```

**Approach 2: Bucket Sort — O(n)**
```python
from collections import Counter

def topKFrequent(nums: list[int], k: int) -> list[int]:
    count = Counter(nums)
    buckets = [[] for _ in range(len(nums) + 1)]
    
    for num, freq in count.items():
        buckets[freq].append(num)
    
    result = []
    for freq in range(len(buckets) - 1, 0, -1):
        for num in buckets[freq]:
            result.append(num)
            if len(result) == k:
                return result
    return result
```

**Time:** O(n) | **Space:** O(n)

### Interview Traps

**Trap 1 — Sorting as final answer**
```python
# ❌ O(n log n) — not optimal
sorted(count.items(), key=lambda x: -x[1])[:k]
```
✅ Present this, then upgrade to bucket sort explaining the bounded frequency insight.

**Trap 2 — Not knowing when to pick heap vs bucket sort**  
- **Heap:** General case, when k << n
- **Bucket Sort:** When frequencies are bounded and O(n) is required  
✅ Articulate this trade-off explicitly — interviewers love it.

**Trap 3 — Off-by-one in bucket indexing**
```python
# ❌ Max frequency = len(nums), causes IndexError
buckets = [[] for _ in range(len(nums))]

# ✅ Allocate len(nums)+1 buckets
buckets = [[] for _ in range(len(nums) + 1)]
```

**Trap 4 — k equals number of unique elements**  
`nums = [1, 2], k = 2` → should return `[1, 2]`. Both approaches handle this — verify aloud.

**Trap 5 — Explicit O(n) requirement**  
*"Can you solve it in O(n) time?"*  
✅ This is exactly bucket sort. Key insight: *"Frequencies are bounded by n, so I can use frequency as an array index."*

---

## 6. Product of Array Except Self

**LeetCode #238 | Difficulty: 🟡 Medium**

### Problem Statement

Given an integer array `nums`, return an array `answer` where `answer[i]` is the product of all elements except `nums[i]`. You must solve it without using division and in O(n) time.

```
Input:  nums = [1,2,3,4]
Output: [24,12,8,6]
```

### Intuition

For each index `i`, the answer is: `(product of all elements to the left of i)` × `(product of all elements to the right of i)`.

Two-pass approach:
1. **Left pass:** `left[i]` = product of `nums[0..i-1]`
2. **Right pass:** `right[i]` = product of `nums[i+1..n-1]`
3. `answer[i] = left[i] * right[i]`

**Space optimization:** Use the output array for left products, then apply right products in a second pass using a running variable — no extra arrays needed.

### Solution

```python
def productExceptSelf(nums: list[int]) -> list[int]:
    n = len(nums)
    result = [1] * n
    
    # Left pass: result[i] = product of nums[0..i-1]
    prefix = 1
    for i in range(n):
        result[i] = prefix
        prefix *= nums[i]
    
    # Right pass: multiply by product of nums[i+1..n-1]
    suffix = 1
    for i in range(n - 1, -1, -1):
        result[i] *= suffix
        suffix *= nums[i]
    
    return result
```

**Time:** O(n) | **Space:** O(1) (output array doesn't count)

### Interview Traps

**Trap 1 — Using division**  
Many candidates try: `total_product / nums[i]`. The problem explicitly forbids division.  
✅ Also: division breaks when `nums[i] == 0`.

**Trap 2 — Zeros in the array**  
If you *were* allowed division, zeros break it. The prefix/suffix approach handles zeros naturally without any special casing.  
✅ Walk through `nums = [1, 0, 3, 4]` to verify.

**Trap 3 — Two-zeros edge case (if division were used)**  
With 2+ zeros, every product is 0. The prefix/suffix approach still handles this correctly.

**Trap 4 — Allocating extra left/right arrays**  
The naive two-array approach uses O(n) extra space. The interviewer may ask you to optimize.  
✅ Use a single running `prefix` and `suffix` variable — reduces space to O(1).

**Trap 5 — Off-by-one in prefix/suffix boundaries**  
`left[0]` should be 1 (no elements to the left of index 0). `right[n-1]` should be 1.  
✅ Initialize `prefix = 1` and `suffix = 1` handles both correctly.

---

## 7. Valid Sudoku

**LeetCode #36 | Difficulty: 🟡 Medium**

### Problem Statement

Determine if a 9×9 Sudoku board is valid based on the following rules:
- Each row must contain digits 1–9 with no repetition.
- Each column must contain digits 1–9 with no repetition.
- Each of the nine 3×3 sub-boxes must contain digits 1–9 with no repetition.

Only filled cells need to be validated. The board does not need to be solvable.

### Intuition

Track seen digits separately for each **row**, **column**, and **3×3 box**. For each cell, check if the digit has appeared before in any of these three contexts. If yes → invalid. Use sets keyed by (row index), (col index), and (box index).

**Box index formula:** `box_id = (row // 3, col // 3)` — maps each cell to one of the nine 3×3 boxes.

### Solution

```python
from collections import defaultdict

def isValidSudoku(board: list[list[str]]) -> bool:
    rows = defaultdict(set)
    cols = defaultdict(set)
    boxes = defaultdict(set)
    
    for r in range(9):
        for c in range(9):
            val = board[r][c]
            if val == '.':
                continue
            
            box_id = (r // 3, c // 3)
            
            if val in rows[r] or val in cols[c] or val in boxes[box_id]:
                return False
            
            rows[r].add(val)
            cols[c].add(val)
            boxes[box_id].add(val)
    
    return True
```

**Time:** O(1) — board is always 9×9 | **Space:** O(1)

### Interview Traps

**Trap 1 — Forgetting the 3×3 box constraint**  
Many candidates only check rows and columns.  
✅ Always validate all three: rows, columns, and boxes.

**Trap 2 — Wrong box index formula**
```python
# ❌ Wrong
box_id = (r % 3, c % 3)

# ✅ Correct
box_id = (r // 3, c // 3)
```

**Trap 3 — Treating '.' as a digit**  
✅ Skip empty cells with `if val == '.': continue`.

**Trap 4 — "Does the board need to be solvable?"**  
✅ No — you're only validating the current state, not checking if a solution exists.

**Trap 5 — Trying to be clever with bitmasks**  
Bitmasks work but add complexity. Sets with tuples are cleaner and easier to explain under interview pressure.

---

## 8. Encode and Decode Strings

**LeetCode #271 | Difficulty: 🟡 Medium**

### Problem Statement

Design an algorithm to encode a list of strings to a single string, and decode it back. The encoded string is sent over the network and decoded on the other side.

```
Input:  ["lint","code","love","you"]
Encode: "4#lint4#code4#love3#you"
Decode: ["lint","code","love","you"]
```

### Intuition

Naive delimiters (like `","`) fail when the strings themselves contain that delimiter. The key insight: **length-prefixed encoding**.

For each string, encode it as `len(string) + "#" + string`. During decoding, read the length, skip past `#`, and extract exactly that many characters. No ambiguity possible.

### Solution

```python
class Codec:
    def encode(self, strs: list[str]) -> str:
        return ''.join(f'{len(s)}#{s}' for s in strs)
    
    def decode(self, s: str) -> list[str]:
        result = []
        i = 0
        while i < len(s):
            j = s.index('#', i)          # find the '#' separator
            length = int(s[i:j])         # read the length
            result.append(s[j+1:j+1+length])  # extract exactly 'length' chars
            i = j + 1 + length           # advance past this encoded chunk
        return result
```

**Time:** O(n) encode and decode | **Space:** O(n)

### Interview Traps

**Trap 1 — Using a simple delimiter like ","**  
✅ Immediately counter: *"What if a string contains a comma?"* Length-prefixed encoding solves this.

**Trap 2 — Escaping as an alternative**  
Escaping works (e.g., `\\,` for literal commas) but is more complex to implement correctly.  
✅ Mention it as an alternative, but prefer length-prefix for clarity.

**Trap 3 — Handling empty strings**  
`""` encodes as `"0#"`. Decoding reads length=0, extracts 0 characters → `""`. Works correctly.  
✅ Walk through this edge case in your explanation.

**Trap 4 — Strings containing `#`**  
A string like `"3#abc"` is not a problem — the decoder reads the *first* `#` after the current index using `s.index('#', i)`, and then extracts exactly the declared number of characters. The `#` inside the string is consumed as data.

**Trap 5 — Using `split('#')` in the decoder**  
✅ This breaks for strings containing `#`. Use the index+length approach, not split.

---

## 9. Longest Consecutive Sequence

**LeetCode #128 | Difficulty: 🟡 Medium**

### Problem Statement

Given an unsorted array of integers `nums`, return the length of the longest consecutive elements sequence. Must run in O(n) time.

```
Input:  nums = [100,4,200,1,3,2]
Output: 4  (sequence: [1,2,3,4])

Input:  nums = [0,3,7,2,5,6,4,8,9,1]
Output: 10
```

### Intuition

Sorting gives an O(n log n) solution — not acceptable. For O(n), use a **HashSet**.

Key insight: only start counting a sequence from its **true beginning** — a number `n` is the start of a sequence if `n-1` is NOT in the set. For each starting number, count how far the consecutive run extends. Each number is visited at most twice total across all sequences → O(n).

### Solution

```python
def longestConsecutive(nums: list[int]) -> int:
    num_set = set(nums)
    best = 0
    
    for n in num_set:
        if n - 1 not in num_set:  # n is the start of a sequence
            length = 1
            while n + length in num_set:
                length += 1
            best = max(best, length)
    
    return best
```

**Time:** O(n) | **Space:** O(n)

### Interview Traps

**Trap 1 — Sorting-based solution**
```python
# ❌ O(n log n) — violates the O(n) requirement
nums.sort()
```
✅ The problem explicitly requires O(n). If you sort, the interviewer will ask you to do better.

**Trap 2 — Not checking for sequence start (TLE)**  
Without the `n-1 not in num_set` guard, every number triggers a while loop → O(n²) worst case.  
✅ The start-check is the entire key to O(n) correctness. Explain it clearly.

**Trap 3 — Iterating over `nums` instead of `num_set`**  
If `nums` has duplicates (e.g., `[1,1,1,2]`), iterating over `nums` triggers the while loop multiple times for the same start.  
✅ Convert to a set first and iterate over the set.

**Trap 4 — Empty array**  
`longestConsecutive([])` should return 0. `best = 0` as the initial value handles this correctly.

**Trap 5 — Negative numbers**  
The algorithm works with negatives too. `num_set = {-3,-2,-1,0}` — starting at `-3` (since `-4` is absent), length = 4.  
✅ Verify with this example to show robustness.

---

*[← Back to Index](./index.md) | [Next: Two Pointers →](./02_two_pointers.md)*

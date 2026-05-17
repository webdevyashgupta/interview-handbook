# Hash Table & Heap — Extra Problems

**Grind 169 Supplement | Not in NeetCode 150**  
[← Back to Index](./index.md)

---

## Problems in this Section

| # | Problem | Difficulty |
|---|---------|------------|
| 1 | [Insert Delete GetRandom O(1)](#1-insert-delete-getrandom-o1) | 🟡 Medium |
| 2 | [First Missing Positive](#2-first-missing-positive) | 🔴 Hard |
| 3 | [Top K Frequent Words](#3-top-k-frequent-words) | 🟡 Medium |
| 4 | [Maximum Profit in Job Scheduling](#4-maximum-profit-in-job-scheduling) | 🔴 Hard |

---

## 1. Insert Delete GetRandom O(1)

**LeetCode #380 | Difficulty: 🟡 Medium**

### Problem Statement

Design a data structure that supports `insert`, `remove`, and `getRandom` all in **O(1)** average time. `getRandom` returns a random element with equal probability.

```
RandomizedSet rs = new RandomizedSet()
rs.insert(1)  → true
rs.remove(2)  → false
rs.insert(2)  → true
rs.getRandom()→ 1 or 2 (equal probability)
rs.remove(1)  → true
rs.getRandom()→ 2
```

### Intuition

The trick: combine a **HashMap** (value → index) with a **dynamic array** (list of values).

- `insert`: append to list, store index in map. O(1).
- `remove`: swap the element to remove with the **last** element in the list, update the map, then pop the last element. O(1).
- `getRandom`: use `random.choice` on the list. O(1).

The swap-with-last trick avoids O(n) list removal.

### Solution

```python
import random

class RandomizedSet:
    def __init__(self):
        self.val_to_idx = {}  # value -> index in list
        self.vals = []        # list of values

    def insert(self, val: int) -> bool:
        if val in self.val_to_idx:
            return False
        self.val_to_idx[val] = len(self.vals)
        self.vals.append(val)
        return True

    def remove(self, val: int) -> bool:
        if val not in self.val_to_idx:
            return False
        # Swap with last element
        idx = self.val_to_idx[val]
        last = self.vals[-1]
        self.vals[idx] = last
        self.val_to_idx[last] = idx
        # Remove last
        self.vals.pop()
        del self.val_to_idx[val]
        return True

    def getRandom(self) -> int:
        return random.choice(self.vals)
```

**Time:** O(1) average for all operations | **Space:** O(n)

### Interview Traps

**Trap 1 — Using only a HashSet**
HashSet gives O(1) insert/remove but `getRandom` requires O(n) to pick a random element (can't index by position).

**Trap 2 — Not handling remove of the last element**
When removing the last element, `last == val`, so swapping with itself is a no-op. The code handles this correctly since we update `val_to_idx[last]` before deleting `val_to_idx[val]` — but if `last == val` they're the same entry. Always test this edge case.

**Trap 3 — Forgetting to update the swapped element's index**
After swapping `val` with `last`, update `val_to_idx[last] = idx` — otherwise `last`'s stored index is stale.

**Trap 4 — "What if we allow duplicates?" (LeetCode #381)**
Track counts per value and store multiple indices. `getRandom` picks a random index from all stored indices.

---

## 2. First Missing Positive

**LeetCode #41 | Difficulty: 🔴 Hard**

### Problem Statement

Given an unsorted integer array `nums`, return the smallest missing positive integer. Must run in O(n) time and O(1) space.

```
Input:  nums = [1,2,0]
Output: 3

Input:  nums = [3,4,-1,1]
Output: 2

Input:  nums = [7,8,9,11,12]
Output: 1
```

### Intuition

The answer is always in range `[1, n+1]` — at most one value is missing from `[1..n]`, so the answer can't exceed `n+1`.

Use the array itself as a hash map: for each value `v` where `1 <= v <= n`, mark position `v-1` as "visited" by negating it. After marking, the first index with a positive value is the answer.

### Solution

```python
def firstMissingPositive(nums: list[int]) -> int:
    n = len(nums)

    # Step 1: Clean up — replace non-positives and out-of-range with n+1
    for i in range(n):
        if nums[i] <= 0 or nums[i] > n:
            nums[i] = n + 1

    # Step 2: Mark presence — negate value at index (v-1) for each v in [1..n]
    for i in range(n):
        v = abs(nums[i])
        if 1 <= v <= n:
            nums[v - 1] = -abs(nums[v - 1])

    # Step 3: Find first index that's still positive
    for i in range(n):
        if nums[i] > 0:
            return i + 1

    return n + 1
```

**Time:** O(n) | **Space:** O(1)

### Interview Traps

**Trap 1 — Using a HashSet (O(n) space)**
Works but violates the O(1) space constraint. The in-place marking uses the array itself as storage.

**Trap 2 — Not cleaning up negatives and zeros first**
Values ≤ 0 or > n are irrelevant. Replace them with `n+1` (a safe sentinel) so the negation marking isn't confused by pre-existing negatives.

**Trap 3 — Using `abs()` when reading during marking pass**
Values may have already been negated by previous iterations. Always read `abs(nums[i])` in the marking pass.

**Trap 4 — "Why is the answer always ≤ n+1?"**
With n elements, the values `1..n` can cover at most n positive integers. At least one from `1..n+1` must be missing.

---

## 3. Top K Frequent Words

**LeetCode #692 | Difficulty: 🟡 Medium**

### Problem Statement

Given an array of strings `words` and integer `k`, return the `k` most frequent words. Sort by frequency (descending); ties broken alphabetically.

```
Input:  words = ["i","love","leetcode","i","love","coding"], k = 2
Output: ["i","love"]

Input:  words = ["the","day","is","sunny","the","the","the","sunny","is","is"], k = 4
Output: ["the","is","sunny","day"]
```

### Intuition

Count frequencies. Use a **min-heap of size k** — but the comparison must be: higher frequency first, then alphabetically smaller first (for ties). Since Python's heap is a min-heap, negate frequency and keep word as-is (strings compare alphabetically naturally).

### Solution

```python
import heapq
from collections import Counter

def topKFrequent(words: list[str], k: int) -> list[str]:
    count = Counter(words)

    # Min-heap: (-freq, word) — pop the "least desirable" when over k
    heap = []
    for word, freq in count.items():
        heapq.heappush(heap, (-freq, word))

    return [heapq.heappop(heap)[1] for _ in range(k)]
```

**Cleaner with nlargest:**
```python
return heapq.nlargest(k, count.keys(), key=lambda w: (-count[w], w))
```

**Time:** O(n log k) | **Space:** O(n)

### Interview Traps

**Trap 1 — Not handling tie-breaking correctly**
Higher frequency wins. Among equal frequencies, alphabetically smaller word wins.  
✅ Sort key: `(-freq, word)` — negate freq so higher freq = smaller heap key, word sorts naturally.

**Trap 2 — Confusing with Top K Frequent Elements (LC #347)**
LC #347 returns numbers with no tie-breaking needed. LC #692 needs lexicographic tie-breaking on strings.

**Trap 3 — Using sort() instead of heap**
`sorted(count, key=lambda w: (-count[w], w))[:k]` is O(n log n). Heap is O(n log k) — better when k << n.

**Trap 4 — Bucket sort approach**
Group words by frequency into buckets, then sort within each bucket alphabetically. O(n log n) due to sorting within buckets but O(n) for the bucketing.

---

## 4. Maximum Profit in Job Scheduling

**LeetCode #1235 | Difficulty: 🔴 Hard**

### Problem Statement

Given `n` jobs with `startTime[i]`, `endTime[i]`, and `profit[i]`, find the maximum profit from a subset of non-overlapping jobs.

```
Input:  startTime = [1,2,3,3], endTime = [3,4,5,6], profit = [50,10,40,70]
Output: 120  (job 0 + job 3)
```

### Intuition

**DP + Binary Search.** Sort jobs by end time. `dp[i]` = max profit considering the first `i` jobs.

For each job `i`: either skip it (`dp[i] = dp[i-1]`) or take it (profit of job `i` + best profit from jobs ending ≤ start of job `i`). Use binary search to find the latest job that ends before job `i` starts.

### Solution

```python
import bisect

def jobScheduling(startTime: list[int], endTime: list[int], profit: list[int]) -> int:
    jobs = sorted(zip(endTime, startTime, profit))
    # dp[i] = max profit using first i jobs (1-indexed)
    dp = [0] * (len(jobs) + 1)
    end_times = [0]  # for binary search (end times of processed jobs)

    for i, (end, start, prof) in enumerate(jobs):
        # Find latest job that ends <= start of current job
        j = bisect.bisect_right(end_times, start) - 1
        # Take current job or skip it
        dp[i + 1] = max(dp[i], dp[j] + prof)
        end_times.append(end)

    return dp[len(jobs)]
```

**Time:** O(n log n) | **Space:** O(n)

### Interview Traps

**Trap 1 — Not sorting by end time**
DP relies on jobs being processed in order of their end times — this is the correct ordering for interval scheduling DP.

**Trap 2 — Binary searching on start times instead of end times**
Binary search for the latest job whose **end time** ≤ current job's **start time**. Using wrong arrays gives incorrect results.

**Trap 3 — Off-by-one in binary search**
`bisect_right(end_times, start) - 1` gives the index of the latest compatible job. The `- 1` accounts for 0-indexed `dp` being 1-indexed.

**Trap 4 — Confusing with weighted interval scheduling**
This is weighted interval scheduling (maximize profit). Unweighted interval scheduling (maximize count of non-overlapping jobs) uses a simpler greedy approach (sort by end time, always take the earliest-ending compatible job).

---

gta*[← Back to Index](./index.md) | [Next: Math & DP Extra →](./26_math_dp_extra.md)*
ion pan*[← Back to Index](./index.md) | [Next: Math & DP Extra →](./26_math_dp_extra.md)*

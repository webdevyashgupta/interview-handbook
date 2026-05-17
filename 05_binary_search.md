# Binary Search

**Topic 5 of 18 | NeetCode 150 Interview Handbook**  
[← Back to Index](./index.md)

---

## Problems in this Section

| # | Problem | Difficulty |
|---|---------|------------|
| 28 | [Binary Search](#28-binary-search) | 🟢 Easy |
| 29 | [Search a 2D Matrix](#29-search-a-2d-matrix) | 🟡 Medium |
| 30 | [Koko Eating Bananas](#30-koko-eating-bananas) | 🟡 Medium |
| 31 | [Find Minimum in Rotated Sorted Array](#31-find-minimum-in-rotated-sorted-array) | 🟡 Medium |
| 32 | [Search in Rotated Sorted Array](#32-search-in-rotated-sorted-array) | 🟡 Medium |
| 33 | [Time Based Key-Value Store](#33-time-based-key-value-store) | 🟡 Medium |
| 34 | [Median of Two Sorted Arrays](#34-median-of-two-sorted-arrays) | 🔴 Hard |

---

## 28. Binary Search

**LeetCode #704 | Difficulty: 🟢 Easy**

### Problem Statement

Given a sorted array of distinct integers `nums` and a target, return the index of `target` or `-1` if not found. Must run in O(log n).

```
Input:  nums = [-1,0,3,5,9,12], target = 9
Output: 4

Input:  nums = [-1,0,3,5,9,12], target = 2
Output: -1
```

### Intuition

Repeatedly halve the search space. Compare the middle element to the target:
- Equal → found.
- Middle too small → target is in the right half.
- Middle too large → target is in the left half.

### Solution

```python
def search(nums: list[int], target: int) -> int:
    left, right = 0, len(nums) - 1

    while left <= right:
        mid = left + (right - left) // 2  # avoids integer overflow
        if nums[mid] == target:
            return mid
        elif nums[mid] < target:
            left = mid + 1
        else:
            right = mid - 1

    return -1
```

**Time:** O(log n) | **Space:** O(1)

### Interview Traps

**Trap 1 — Integer overflow in mid calculation**
`mid = (left + right) // 2` overflows in languages with fixed-size integers (Java, C++).  
✅ Use `mid = left + (right - left) // 2`. Python ints don't overflow, but show this habit anyway.

**Trap 2 — Wrong loop condition: `left < right` vs `left <= right`**
`left < right` misses the case where the target is the last remaining element (when `left == right`).  
✅ Use `left <= right` for standard binary search.

**Trap 3 — Not updating pointers past mid**
```python
# ❌ Infinite loop when left == mid
left = mid
right = mid

# ✅ Always move past mid
left = mid + 1
right = mid - 1
```

**Trap 4 — Assuming the array has duplicates**
The problem guarantees distinct integers. If duplicates are allowed, `return mid` might not return the first/last occurrence — binary search variants are needed.

**Trap 5 — Forgetting to return -1**
If the loop exits without finding the target, explicitly return `-1`.

---

## 29. Search a 2D Matrix

**LeetCode #74 | Difficulty: 🟡 Medium**

### Problem Statement

Given an `m x n` matrix where each row is sorted and the first integer of each row is greater than the last of the previous row, search for a `target` value efficiently.

```
Input:  matrix = [[1,3,5,7],[10,11,16,20],[23,30,34,60]], target = 3
Output: true
```

### Intuition

The matrix is essentially a sorted 1D array laid out in rows. Treat it as such — run binary search on indices `0` to `m*n - 1`. Convert a 1D index `mid` to 2D coordinates: `row = mid // n`, `col = mid % n`.

### Solution

```python
def searchMatrix(matrix: list[list[int]], target: int) -> bool:
    m, n = len(matrix), len(matrix[0])
    left, right = 0, m * n - 1

    while left <= right:
        mid = left + (right - left) // 2
        val = matrix[mid // n][mid % n]
        if val == target:
            return True
        elif val < target:
            left = mid + 1
        else:
            right = mid - 1

    return False
```

**Time:** O(log(m·n)) | **Space:** O(1)

### Interview Traps

**Trap 1 — Two separate binary searches (row then column)**
Searching for the row first and then binary searching within it works but is O(log m + log n). The flat binary search is O(log(m·n)) = O(log m + log n) — same asymptotically, but cleaner.

**Trap 2 — Wrong index conversion**
```python
row = mid // n  # integer divide by number of COLUMNS
col = mid % n   # modulo number of COLUMNS
```
A common mistake is dividing/modding by `m` (rows) instead of `n` (columns).

**Trap 3 — Confusing with Search a 2D Matrix II (LeetCode #240)**
Matrix II has rows and columns sorted but the first element of each row is NOT necessarily greater than the last of the previous row. The flat binary search doesn't apply there — use the staircase search from top-right instead.

**Trap 4 — Empty matrix**
Check `if not matrix or not matrix[0]: return False` before proceeding.

---

## 30. Koko Eating Bananas

**LeetCode #875 | Difficulty: 🟡 Medium**

### Problem Statement

Koko has `piles` of bananas and `h` hours to eat them all. Each hour she eats at most `k` bananas from one pile. Find the minimum integer `k` such that she can finish in `h` hours.

```
Input:  piles = [3,6,7,11], h = 8
Output: 4
```

### Intuition

Binary search on the **answer** (the eating speed `k`), not on the input array. The search space is `[1, max(piles)]`. For a given `k`, compute the total hours needed: `sum(ceil(pile / k) for pile in piles)`. If hours ≤ h, `k` is feasible — try smaller. If hours > h, need to go faster.

### Solution

```python
import math

def minEatingSpeed(piles: list[int], h: int) -> int:
    left, right = 1, max(piles)

    while left < right:
        mid = left + (right - left) // 2
        hours = sum(math.ceil(pile / mid) for pile in piles)
        if hours <= h:
            right = mid       # feasible, try smaller
        else:
            left = mid + 1    # too slow, need faster

    return left
```

**Time:** O(n log(max(piles))) | **Space:** O(1)

### Interview Traps

**Trap 1 — Not recognizing this as a binary search problem**
There's no sorted array to search — the binary search is on the *answer space*.  
✅ Any time you're minimizing/maximizing a value with a monotonic feasibility condition, think binary search on the answer.

**Trap 2 — Wrong search bounds**
- Lower bound: `1` (must eat at least 1 banana/hour)
- Upper bound: `max(piles)` (eating the largest pile in one hour is always sufficient)

**Trap 3 — Using `left <= right` instead of `left < right`**
With the `right = mid` update (not `mid - 1`), using `left <= right` causes infinite loops.  
✅ Use `left < right` and return `left` at the end.

**Trap 4 — Integer ceiling without math.ceil**
```python
# These are equivalent for positive integers:
math.ceil(pile / mid)
(pile + mid - 1) // mid  # integer arithmetic version
```

**Trap 5 — "Why does the feasibility function have a monotonic property?"**
If speed `k` is feasible (finishes in ≤ h hours), then any speed `k' > k` is also feasible. This monotonicity is what makes binary search valid.

---

## 31. Find Minimum in Rotated Sorted Array

**LeetCode #153 | Difficulty: 🟡 Medium**

### Problem Statement

Given a sorted array rotated at some unknown pivot, find the minimum element. All integers are unique.

```
Input:  nums = [3,4,5,1,2]
Output: 1

Input:  nums = [4,5,6,7,0,1,2]
Output: 0
```

### Intuition

The array has two sorted halves. The minimum is at the start of the right (smaller) half. Use binary search:
- If `nums[mid] > nums[right]`, the minimum is in the right half → `left = mid + 1`.
- Otherwise, the minimum is in the left half (including mid) → `right = mid`.

### Solution

```python
def findMin(nums: list[int]) -> int:
    left, right = 0, len(nums) - 1

    while left < right:
        mid = left + (right - left) // 2
        if nums[mid] > nums[right]:
            left = mid + 1
        else:
            right = mid

    return nums[left]
```

**Time:** O(log n) | **Space:** O(1)

### Interview Traps

**Trap 1 — Comparing mid to left instead of right**
Comparing to `nums[left]` is ambiguous after rotation. Comparing to `nums[right]` cleanly separates the two halves.  
✅ Always compare `nums[mid]` to `nums[right]`.

**Trap 2 — Using `left <= right` causing infinite loop**
With `right = mid` (not `mid - 1`), `left <= right` loops forever when `left == right`.  
✅ Use `left < right`; return `nums[left]` at the end.

**Trap 3 — Array not rotated (already sorted)**
If the array was rotated 0 times, `nums[mid] <= nums[right]` always, and `right` converges to 0. The minimum at index 0 is correctly returned.

**Trap 4 — Duplicate elements follow-up (LeetCode #154)**
With duplicates, `nums[mid] == nums[right]` is ambiguous — can't determine which half to search.  
✅ In that case: decrement `right` by 1 and continue. Time degrades to O(n) worst case.

---

## 32. Search in Rotated Sorted Array

**LeetCode #33 | Difficulty: 🟡 Medium**

### Problem Statement

Given a rotated sorted array with unique values and a target, return the target's index or `-1` if not found.

```
Input:  nums = [4,5,6,7,0,1,2], target = 0
Output: 4

Input:  nums = [4,5,6,7,0,1,2], target = 3
Output: -1
```

### Intuition

At any `mid`, one half is always sorted. Determine which half is sorted, then check if the target falls within it. Search that half if yes; search the other half if no.

### Solution

```python
def search(nums: list[int], target: int) -> int:
    left, right = 0, len(nums) - 1

    while left <= right:
        mid = left + (right - left) // 2

        if nums[mid] == target:
            return mid

        # Left half is sorted
        if nums[left] <= nums[mid]:
            if nums[left] <= target < nums[mid]:
                right = mid - 1
            else:
                left = mid + 1
        # Right half is sorted
        else:
            if nums[mid] < target <= nums[right]:
                left = mid + 1
            else:
                right = mid - 1

    return -1
```

**Time:** O(log n) | **Space:** O(1)

### Interview Traps

**Trap 1 — Not identifying which half is sorted**
This is the crux. One half is always sorted after rotation — the pivot is only in one half.  
✅ Check `nums[left] <= nums[mid]` to identify the sorted half.

**Trap 2 — Range check errors (off-by-one)**
The target range check must be inclusive on the correct ends:
- Left sorted: `nums[left] <= target < nums[mid]`
- Right sorted: `nums[mid] < target <= nums[right]`

**Trap 3 — Handling `nums[left] <= nums[mid]` with equality**
When `left == mid`, `nums[left] == nums[mid]` — the left half is technically "sorted" (single element). The `<=` handles this correctly.

**Trap 4 — "What if there are duplicates?" (LeetCode #81)**
With duplicates, `nums[left] == nums[mid]` is ambiguous. Must `left++` and retry — degrades to O(n) worst case.

---

## 33. Time Based Key-Value Store

**LeetCode #981 | Difficulty: 🟡 Medium**

### Problem Statement

Design a data structure that stores key-value pairs with timestamps and retrieves the value with the largest timestamp ≤ the given timestamp.

```
store.set("foo", "bar", 1)
store.get("foo", 1)  → "bar"
store.get("foo", 3)  → "bar"
store.set("foo", "bar2", 4)
store.get("foo", 4)  → "bar2"
store.get("foo", 5)  → "bar2"
```

### Intuition

Store values in a dict of `key → list of (timestamp, value)` pairs. Since `set` is called with strictly increasing timestamps, the list is always sorted by timestamp. Use binary search to find the largest timestamp ≤ query timestamp.

### Solution

```python
from collections import defaultdict
import bisect

class TimeMap:
    def __init__(self):
        self.store = defaultdict(list)  # key -> [(timestamp, value)]

    def set(self, key: str, value: str, timestamp: int) -> None:
        self.store[key].append((timestamp, value))

    def get(self, key: str, timestamp: int) -> str:
        if key not in self.store:
            return ""
        entries = self.store[key]
        # Binary search: find rightmost timestamp <= given timestamp
        lo, hi = 0, len(entries) - 1
        result = ""
        while lo <= hi:
            mid = lo + (hi - lo) // 2
            if entries[mid][0] <= timestamp:
                result = entries[mid][1]
                lo = mid + 1
            else:
                hi = mid - 1
        return result
```

**Time:** O(1) set, O(log n) get | **Space:** O(n)

### Interview Traps

**Trap 1 — Linear scan for get**
Scanning all timestamps is O(n) per get. Binary search reduces to O(log n).  
✅ The sorted-by-construction property of timestamps is the key insight.

**Trap 2 — Returning empty string vs None**
The problem requires returning `""` when no valid timestamp exists.

**Trap 3 — Searching for exact timestamp only**
The query asks for the largest timestamp ≤ given timestamp, not exact equality. Use "find rightmost ≤" binary search pattern.

**Trap 4 — Using `bisect_right` correctly**
```python
# Alternative using bisect:
idx = bisect.bisect_right(entries, (timestamp, chr(127))) - 1
return entries[idx][1] if idx >= 0 else ""
```
`chr(127)` is a high sentinel so `(timestamp, chr(127))` sorts after all real values at the same timestamp.

**Trap 5 — Key doesn't exist**
Always check `if key not in self.store: return ""` before accessing the list.

---

## 34. Median of Two Sorted Arrays

**LeetCode #4 | Difficulty: 🔴 Hard**

### Problem Statement

Given two sorted arrays `nums1` and `nums2`, return the median of the combined sorted array. Must run in O(log(m+n)).

```
Input:  nums1 = [1,3], nums2 = [2]
Output: 2.0

Input:  nums1 = [1,2], nums2 = [3,4]
Output: 2.5
```

### Intuition

Binary search on the **partition point** of the smaller array. A valid partition splits both arrays such that all elements on the left are ≤ all elements on the right. The median is derived from the boundary elements.

For a combined array of size `n`, the left half has `(n+1)//2` elements. If we take `i` elements from `nums1`, we take `(n+1)//2 - i` from `nums2`. Adjust `i` until the partition is valid.

### Solution

```python
def findMedianSortedArrays(nums1: list[int], nums2: list[int]) -> float:
    # Ensure nums1 is the smaller array
    if len(nums1) > len(nums2):
        nums1, nums2 = nums2, nums1

    m, n = len(nums1), len(nums2)
    half = (m + n + 1) // 2
    left, right = 0, m

    while left <= right:
        i = (left + right) // 2   # partition in nums1
        j = half - i               # partition in nums2

        nums1_left  = nums1[i-1] if i > 0 else float('-inf')
        nums1_right = nums1[i]   if i < m else float('inf')
        nums2_left  = nums2[j-1] if j > 0 else float('-inf')
        nums2_right = nums2[j]   if j < n else float('inf')

        if nums1_left <= nums2_right and nums2_left <= nums1_right:
            # Valid partition
            max_left = max(nums1_left, nums2_left)
            if (m + n) % 2 == 1:
                return float(max_left)
            min_right = min(nums1_right, nums2_right)
            return (max_left + min_right) / 2.0
        elif nums1_left > nums2_right:
            right = i - 1
        else:
            left = i + 1

    return 0.0
```

**Time:** O(log(min(m, n))) | **Space:** O(1)

### Interview Traps

**Trap 1 — Merging and finding median (O(m+n))**
This violates the O(log(m+n)) requirement.  
✅ The interviewer explicitly requires logarithmic time — binary search on the partition is mandatory.

**Trap 2 — Not binary searching on the smaller array**
Always binary search on the smaller array to keep the partition valid (`j = half - i` must stay in bounds for `nums2`).

**Trap 3 — Handling edge partitions with ±infinity sentinels**
When `i == 0`, `nums1_left` is `-inf` (no elements on left from nums1). When `i == m`, `nums1_right` is `+inf`.  
✅ Always use sentinels for boundary conditions.

**Trap 4 — Even vs odd total length**
- Odd total: median is `max(nums1_left, nums2_left)`
- Even total: median is `(max(left) + min(right)) / 2`  
✅ Handle both cases in the valid-partition block.

**Trap 5 — "Explain the invariant"**
Be ready to explain: *"I'm looking for a partition where every element on the left side is ≤ every element on the right side. The valid partition condition is `nums1_left <= nums2_right` and `nums2_left <= nums1_right`."*

---

*[← Back to Index](./index.md) | [Next: Linked List →](./06_linked_list.md)*

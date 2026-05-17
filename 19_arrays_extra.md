# Arrays — Extra Problems

**Grind 169 Supplement | Not in NeetCode 150**  
[← Back to Index](./index.md)

---

## Problems in this Section

| # | Problem | Difficulty |
|---|---------|------------|
| 1 | [Move Zeroes](#1-move-zeroes) | 🟢 Easy |
| 2 | [Squares of a Sorted Array](#2-squares-of-a-sorted-array) | 🟢 Easy |
| 3 | [Rotate Array](#3-rotate-array) | 🟡 Medium |
| 4 | [Contiguous Array](#4-contiguous-array) | 🟡 Medium |
| 5 | [Subarray Sum Equals K](#5-subarray-sum-equals-k) | 🟡 Medium |
| 6 | [3Sum Closest](#6-3sum-closest) | 🟡 Medium |

---

## 1. Move Zeroes

**LeetCode #283 | Difficulty: 🟢 Easy**

### Problem Statement

Given an integer array `nums`, move all `0`s to the end while maintaining the relative order of non-zero elements. Do it in-place.

```
Input:  nums = [0,1,0,3,12]
Output: [1,3,12,0,0]

Input:  nums = [0]
Output: [0]
```

### Intuition

Use a **slow pointer** `insert_pos` that tracks where the next non-zero element should go. Scan with a fast pointer — whenever a non-zero is found, swap it to `insert_pos` and advance it. All zeros naturally shift to the end.

### Solution

```python
def moveZeroes(nums: list[int]) -> None:
    insert_pos = 0
    for i in range(len(nums)):
        if nums[i] != 0:
            nums[insert_pos], nums[i] = nums[i], nums[insert_pos]
            insert_pos += 1
```

**Time:** O(n) | **Space:** O(1)

### Interview Traps

**Trap 1 — Creating a new array**
The problem requires in-place modification. Don't build a new list.

**Trap 2 — Not maintaining relative order**
Simply counting zeros and filling the end destroys relative order of non-zeros. The two-pointer swap preserves it.

**Trap 3 — Unnecessary swaps when insert_pos == i**
When no zeros have been encountered yet, `insert_pos == i` and you're swapping an element with itself. It's harmless but can be optimized:
```python
if i != insert_pos:
    nums[insert_pos], nums[i] = nums[i], nums[insert_pos]
```

**Trap 4 — "What if we want to move a different value instead of zeros?"**
Generalize: replace `!= 0` with `!= target_val`. Same logic applies.

---

## 2. Squares of a Sorted Array

**LeetCode #977 | Difficulty: 🟢 Easy**

### Problem Statement

Given an integer array `nums` sorted in non-decreasing order, return an array of the squares of each number, sorted in non-decreasing order.

```
Input:  nums = [-4,-1,0,3,10]
Output: [0,1,9,16,100]

Input:  nums = [-7,-3,2,3,11]
Output: [4,9,9,49,121]
```

### Intuition

Squaring destroys the sorted order for negative numbers. The largest squares come from either end (most negative or most positive). Use **two pointers** from both ends, comparing absolute values, and fill the result array from right to left.

### Solution

```python
def sortedSquares(nums: list[int]) -> list[int]:
    n = len(nums)
    result = [0] * n
    left, right = 0, n - 1
    pos = n - 1

    while left <= right:
        if abs(nums[left]) > abs(nums[right]):
            result[pos] = nums[left] ** 2
            left += 1
        else:
            result[pos] = nums[right] ** 2
            right -= 1
        pos -= 1

    return result
```

**Time:** O(n) | **Space:** O(n)

### Interview Traps

**Trap 1 — Squaring then sorting**
`[x**2 for x in nums]` then `.sort()` is O(n log n). The two-pointer approach is O(n) — always mention this improvement.

**Trap 2 — Filling result left to right**
The largest squares come from the ends. Fill the result **right to left** (largest first).

**Trap 3 — Handling all-negative or all-positive arrays**
The algorithm handles both correctly — the two-pointer naturally converges regardless.

**Trap 4 — Integer overflow**
In Python, no issue. In Java/C++, use `long` for intermediate squares if values are large.

---

## 3. Rotate Array

**LeetCode #189 | Difficulty: 🟡 Medium**

### Problem Statement

Given an integer array `nums`, rotate the array to the right by `k` steps.

```
Input:  nums = [1,2,3,4,5,6,7], k = 3
Output: [5,6,7,1,2,3,4]

Input:  nums = [-1,-100,3,99], k = 2
Output: [3,99,-1,-100]
```

### Intuition

**Reversal trick:** Rotating right by `k` is equivalent to:
1. Reverse the entire array
2. Reverse the first `k` elements
3. Reverse the remaining `n-k` elements

This achieves O(1) space without extra arrays.

### Solution

```python
def rotate(nums: list[int], k: int) -> None:
    n = len(nums)
    k %= n  # handle k > n

    def reverse(left, right):
        while left < right:
            nums[left], nums[right] = nums[right], nums[left]
            left += 1
            right -= 1

    reverse(0, n - 1)    # reverse all
    reverse(0, k - 1)    # reverse first k
    reverse(k, n - 1)    # reverse rest
```

**Time:** O(n) | **Space:** O(1)

**Slice approach (O(n) space):**
```python
k %= len(nums)
nums[:] = nums[-k:] + nums[:-k]
```

### Interview Traps

**Trap 1 — Not handling k > n**
`k = 10` on an array of length 7 is the same as `k = 3`. Always `k %= n` first.

**Trap 2 — k = 0 or k = n**
Both result in no rotation. `k %= n` handles `k = n` → `k = 0` automatically.

**Trap 3 — Using a cyclic replacements approach**
Another O(1) space approach: follow cyclic chains of replacements. More complex to implement correctly — the reversal trick is cleaner.

**Trap 4 — Modifying `nums[:]` vs `nums`**
`nums = nums[-k:] + nums[:-k]` creates a new local variable — doesn't modify in place.  
✅ Use `nums[:] = ...` to modify in place.

---

## 4. Contiguous Array

**LeetCode #525 | Difficulty: 🟡 Medium**

### Problem Statement

Given a binary array `nums`, return the maximum length of a contiguous subarray with an equal number of `0`s and `1`s.

```
Input:  nums = [0,1]
Output: 2

Input:  nums = [0,1,0]
Output: 2
```

### Intuition

Replace every `0` with `-1`. Now the problem becomes: find the longest subarray with sum = 0 — a classic **prefix sum + HashMap** problem.

Track `prefix_sum → first_index_seen`. If the same prefix sum appears again, the subarray between those two indices has sum 0 (equal 0s and 1s).

### Solution

```python
def findMaxLength(nums: list[int]) -> int:
    prefix_sum = 0
    best = 0
    seen = {0: -1}  # prefix_sum -> earliest index

    for i, num in enumerate(nums):
        prefix_sum += 1 if num == 1 else -1

        if prefix_sum in seen:
            best = max(best, i - seen[prefix_sum])
        else:
            seen[prefix_sum] = i

    return best
```

**Time:** O(n) | **Space:** O(n)

### Interview Traps

**Trap 1 — Not initializing `seen = {0: -1}`**
Without the base case, a subarray starting from index 0 with sum 0 wouldn't be counted.  
✅ Initialize with `{0: -1}` — representing a "virtual" prefix of sum 0 before the array starts.

**Trap 2 — Updating seen even when prefix_sum is already in it**
Only store the **first** occurrence of each prefix sum — we want the maximum length, so the earliest start is best.  
✅ Only add to `seen` when the key doesn't already exist.

**Trap 3 — Not transforming 0s to -1s**
Without the transformation, a sum of 0 doesn't mean equal counts. The -1 substitution is the entire key insight.

**Trap 4 — Confusing with Maximum Subarray (Kadane's)**
This problem finds a subarray with a specific sum (0), not the maximum sum. Different pattern entirely.

---

## 5. Subarray Sum Equals K

**LeetCode #560 | Difficulty: 🟡 Medium**

### Problem Statement

Given an integer array `nums` and integer `k`, return the total number of subarrays whose sum equals `k`.

```
Input:  nums = [1,1,1], k = 2
Output: 2

Input:  nums = [1,2,3], k = 3
Output: 2
```

### Intuition

Use **prefix sums**. If `prefix[j] - prefix[i] == k`, then the subarray `nums[i+1..j]` sums to `k`. Rearranging: `prefix[i] == prefix[j] - k`.

As we build the prefix sum, check how many times `prefix - k` has appeared before — each occurrence corresponds to a valid subarray ending at the current index.

### Solution

```python
from collections import defaultdict

def subarraySum(nums: list[int], k: int) -> int:
    count = 0
    prefix = 0
    freq = defaultdict(int)
    freq[0] = 1  # empty prefix

    for num in nums:
        prefix += num
        count += freq[prefix - k]
        freq[prefix] += 1

    return count
```

**Time:** O(n) | **Space:** O(n)

### Interview Traps

**Trap 1 — Using a sliding window**
Sliding window only works for non-negative numbers. This problem can have negative numbers — prefix sum + HashMap is required.

**Trap 2 — Not initializing `freq[0] = 1`**
Without this, subarrays starting from index 0 are missed.

**Trap 3 — Checking freq after updating prefix**
Always check `freq[prefix - k]` BEFORE incrementing `freq[prefix]` — otherwise a single element equal to `k` would incorrectly count itself.  
✅ Check → then update (as shown in the solution).

**Trap 4 — Confusing count of subarrays with indices of subarrays**
This problem asks for the count, not the actual subarrays. For the actual subarrays, store the indices instead.

---

## 6. 3Sum Closest

**LeetCode #16 | Difficulty: 🟡 Medium**

### Problem Statement

Given an integer array `nums` and a target, return the sum of three integers such that the sum is closest to the target.

```
Input:  nums = [-1,2,1,-4], target = 1
Output: 2  (sum of -1+2+1=2)

Input:  nums = [0,0,0], target = 1
Output: 0
```

### Intuition

Same structure as 3Sum — sort, fix one element, use two pointers for the rest. Track the closest sum seen so far. Move pointers based on whether the current sum is less than or greater than target.

### Solution

```python
def threeSumClosest(nums: list[int], target: int) -> int:
    nums.sort()
    closest = float('inf')

    for i in range(len(nums) - 2):
        left, right = i + 1, len(nums) - 1

        while left < right:
            s = nums[i] + nums[left] + nums[right]

            if abs(s - target) < abs(closest - target):
                closest = s

            if s < target:
                left += 1
            elif s > target:
                right -= 1
            else:
                return s  # exact match

    return closest
```

**Time:** O(n²) | **Space:** O(1)

### Interview Traps

**Trap 1 — Not sorting first**
Two pointers require a sorted array. Always sort first.

**Trap 2 — Not handling exact match**
If `s == target`, return immediately — it's the closest possible.

**Trap 3 — Wrong closest comparison**
Compare `abs(s - target) < abs(closest - target)`, not just `s - target`.

**Trap 4 — Confusing with 3Sum (return triplets)**
3Sum returns all zero-sum triplets. 3Sum Closest returns a single sum value. Different output, same two-pointer technique.

---

*[← Back to Index](./index.md) | [Next: Linked List Extra →](./20_linked_list_extra.md)*

# Two Pointers

**Topic 2 of 18 | NeetCode 150 Interview Handbook**  
[← Back to Index](./index.md)

---

## Problems in this Section

| # | Problem | Difficulty |
|---|---------|------------|
| 10 | [Valid Palindrome](#10-valid-palindrome) | 🟢 Easy |
| 11 | [Two Sum II – Input Array Is Sorted](#11-two-sum-ii--input-array-is-sorted) | 🟡 Medium |
| 12 | [3Sum](#12-3sum) | 🟡 Medium |
| 13 | [Container With Most Water](#13-container-with-most-water) | 🟡 Medium |
| 14 | [Trapping Rain Water](#14-trapping-rain-water) | 🔴 Hard |

---

## 10. Valid Palindrome

**LeetCode #125 | Difficulty: 🟢 Easy**

### Problem Statement

A phrase is a palindrome if, after converting all uppercase letters to lowercase and removing all non-alphanumeric characters, it reads the same forward and backward.

Given a string `s`, return `true` if it is a palindrome, or `false` otherwise.

```
Input:  s = "A man, a plan, a canal: Panama"
Output: true

Input:  s = "race a car"
Output: false
```

### Intuition

Use two pointers — one starting from the left, one from the right. Skip non-alphanumeric characters, compare characters (case-insensitive). If any mismatch is found → not a palindrome. If pointers meet → palindrome confirmed.

This avoids creating a cleaned copy of the string, saving O(n) extra space.

### Solution

```python
def isPalindrome(s: str) -> bool:
    left, right = 0, len(s) - 1

    while left < right:
        while left < right and not s[left].isalnum():
            left += 1
        while left < right and not s[right].isalnum():
            right -= 1

        if s[left].lower() != s[right].lower():
            return False

        left += 1
        right -= 1

    return True
```

**Time:** O(n) | **Space:** O(1)

**Clean but O(n) space alternative:**
```python
cleaned = ''.join(c.lower() for c in s if c.isalnum())
return cleaned == cleaned[::-1]
```

### Interview Traps

**Trap 1 — Building a cleaned string first**
The cleaned-string approach is readable, but uses O(n) space. If the interviewer asks for O(1) space, switch to the two-pointer approach.  
✅ Offer both; know when to use each.

**Trap 2 — Forgetting to skip non-alphanumeric characters**
A common bug: pointers advance without skipping punctuation/spaces, causing incorrect comparisons.  
✅ Both inner `while` loops must guard `left < right` to avoid crossing.

**Trap 3 — Case sensitivity**
Comparing `s[left] != s[right]` without lowercasing will incorrectly flag `'A'` vs `'a'`.  
✅ Always normalize: `s[left].lower() == s[right].lower()`.

**Trap 4 — Empty string**
`""` is considered a palindrome (vacuously true). The loop never executes and returns `True`. Verify this aloud.

**Trap 5 — Single character**
A single character is always a palindrome. The loop condition `left < right` exits immediately.

---

## 11. Two Sum II – Input Array Is Sorted

**LeetCode #167 | Difficulty: 🟡 Medium**

### Problem Statement

Given a **1-indexed** sorted array `numbers` and a `target`, return the indices of the two numbers that add up to `target`. Exactly one solution exists; use only O(1) extra space.

```
Input:  numbers = [2,7,11,15], target = 9
Output: [1, 2]

Input:  numbers = [2,3,4], target = 6
Output: [1, 3]
```

### Intuition

Because the array is **sorted**, we can use two pointers at the extremes. Their sum tells us which direction to move:
- Sum **too small** → move left pointer right (increase sum)
- Sum **too large** → move right pointer left (decrease sum)
- Sum **equals target** → return both indices

This eliminates the need for a HashMap, achieving O(1) extra space.

### Solution

```python
def twoSum(numbers: list[int], target: int) -> list[int]:
    left, right = 0, len(numbers) - 1

    while left < right:
        s = numbers[left] + numbers[right]
        if s == target:
            return [left + 1, right + 1]  # 1-indexed
        elif s < target:
            left += 1
        else:
            right -= 1

    return []
```

**Time:** O(n) | **Space:** O(1)

### Interview Traps

**Trap 1 — Using a HashMap (ignoring the sorted property)**
HashMap works, but wastes the sorted structure and uses O(n) space — the problem explicitly says O(1).  
✅ Always leverage given constraints. Sorted array → two pointers.

**Trap 2 — Returning 0-indexed instead of 1-indexed**
The problem is 1-indexed. A common off-by-one bug.  
✅ Return `[left + 1, right + 1]`.

**Trap 3 — Infinite loop**
If neither pointer moves (impossible given the constraints, but verify your logic), the loop never ends.  
✅ Exactly one solution is guaranteed — the loop always terminates.

**Trap 4 — "What if there are multiple valid pairs?"**
✅ Collect all pairs — don't return immediately; continue after finding a match. Switch to a results list.

**Trap 5 — Confusing with unsorted Two Sum**
Two Sum (LeetCode #1) needs a HashMap because the array isn't sorted. Two Sum II doesn't — draw this distinction explicitly to show depth of understanding.

---

## 12. 3Sum

**LeetCode #15 | Difficulty: 🟡 Medium**

### Problem Statement

Given an integer array `nums`, return all triplets `[nums[i], nums[j], nums[k]]` such that `i != j`, `i != k`, `j != k`, and `nums[i] + nums[j] + nums[k] == 0`. The solution set must not contain duplicate triplets.

```
Input:  nums = [-1,0,1,2,-1,-4]
Output: [[-1,-1,2],[-1,0,1]]

Input:  nums = [0,0,0]
Output: [[0,0,0]]
```

### Intuition

Sort the array. Fix the first element `nums[i]`, then run Two Sum II (two pointers) on the remaining subarray to find pairs that sum to `-nums[i]`.

Key: **skip duplicates** at every level — skip duplicate values of `nums[i]`, and skip duplicate values of left/right pointers after finding a valid triplet.

### Solution

```python
def threeSum(nums: list[int]) -> list[list[int]]:
    nums.sort()
    result = []

    for i in range(len(nums) - 2):
        if i > 0 and nums[i] == nums[i - 1]:
            continue  # skip duplicate first elements

        left, right = i + 1, len(nums) - 1

        while left < right:
            s = nums[i] + nums[left] + nums[right]
            if s == 0:
                result.append([nums[i], nums[left], nums[right]])
                while left < right and nums[left] == nums[left + 1]:
                    left += 1
                while left < right and nums[right] == nums[right - 1]:
                    right -= 1
                left += 1
                right -= 1
            elif s < 0:
                left += 1
            else:
                right -= 1

    return result
```

**Time:** O(n²) | **Space:** O(1) excluding output

### Interview Traps

**Trap 1 — Not sorting first**
Two pointers only work on a sorted array. Without sorting, the approach is invalid.  
✅ Sorting is the mandatory first step.

**Trap 2 — Duplicate triplets in output**
Without skipping duplicates, `[-1,-1,2]` might appear multiple times.  
✅ Three places to skip duplicates:
1. `if i > 0 and nums[i] == nums[i-1]: continue`
2. Skip duplicate `left` values after a valid triplet
3. Skip duplicate `right` values after a valid triplet

**Trap 3 — Early termination**
Once `nums[i] > 0`, no triplet can sum to 0 (all elements to the right are ≥ nums[i] > 0).  
✅ Add `if nums[i] > 0: break` for a minor optimization.

**Trap 4 — Returning indices instead of values**
Unlike Two Sum, 3Sum asks for the **values**, not indices.  
✅ Append `[nums[i], nums[left], nums[right]]`.

**Trap 5 — Brute force O(n³) as final answer**
✅ Sort + two-pointer reduces to O(n²). Always reach this as your final answer.

---

## 13. Container With Most Water

**LeetCode #11 | Difficulty: 🟡 Medium**

### Problem Statement

Given an integer array `height` of length `n` where each element represents a vertical line at position `i`, find two lines that together with the x-axis form a container that holds the most water.

```
Input:  height = [1,8,6,2,5,4,8,3,7]
Output: 49
```

### Intuition

Water held = `min(height[left], height[right]) * (right - left)`.

Use two pointers at the extremes. At each step, move the pointer pointing to the **shorter line** inward — the only hope for a larger container is a taller line; the width can only decrease from here, so moving the taller pointer would strictly reduce area.

### Solution

```python
def maxArea(height: list[int]) -> int:
    left, right = 0, len(height) - 1
    max_water = 0

    while left < right:
        water = min(height[left], height[right]) * (right - left)
        max_water = max(max_water, water)

        if height[left] < height[right]:
            left += 1
        else:
            right -= 1

    return max_water
```

**Time:** O(n) | **Space:** O(1)

### Interview Traps

**Trap 1 — Brute force O(n²)**
Checking every pair works but is too slow.  
✅ The interviewer expects the greedy two-pointer approach.

**Trap 2 — Moving the wrong pointer**
Moving the taller pointer inward can only decrease area (shorter width, height limited by the same or shorter line).  
✅ Always move the pointer at the **shorter** line. If equal, either pointer is fine.

**Trap 3 — Not updating max_water before moving**
Always compute area before deciding which pointer to move.

**Trap 4 — Confusing with Trapping Rain Water**
Container With Most Water: two boundary lines, no fill between.  
Trapping Rain Water: water fills every valley between bars.  
✅ These are different problems — distinguish them clearly.

**Trap 5 — "Why is this greedy approach correct?"**
The interviewer may ask you to prove it. The key argument: when we move the shorter pointer, we're discarding all pairs involving the current shorter line — we've already seen the best such pair (maximum width), and any future pair with this line has smaller width and the same or smaller height.

---

## 14. Trapping Rain Water

**LeetCode #42 | Difficulty: 🔴 Hard**

### Problem Statement

Given `n` non-negative integers representing an elevation map where the width of each bar is 1, compute how much water it can trap after raining.

```
Input:  height = [0,1,0,2,1,0,1,3,2,1,2,1]
Output: 6

Input:  height = [4,2,0,3,2,5]
Output: 9
```

### Intuition

Water at position `i` = `min(max_left[i], max_right[i]) - height[i]`

Where `max_left[i]` = tallest bar to the left of `i`, `max_right[i]` = tallest bar to the right of `i`.

**Approach 1 (Precompute arrays):** Build left-max and right-max arrays in two passes, then compute water in a third pass. O(n) time, O(n) space.

**Approach 2 (Two pointers — optimal):** Maintain running `left_max` and `right_max`. Process from whichever side has the smaller max — water at that position is determined entirely by that side. O(n) time, O(1) space.

### Solution

**Approach 1: Prefix/Suffix Arrays**
```python
def trap(height: list[int]) -> int:
    n = len(height)
    left_max = [0] * n
    right_max = [0] * n

    left_max[0] = height[0]
    for i in range(1, n):
        left_max[i] = max(left_max[i-1], height[i])

    right_max[n-1] = height[n-1]
    for i in range(n-2, -1, -1):
        right_max[i] = max(right_max[i+1], height[i])

    water = 0
    for i in range(n):
        water += min(left_max[i], right_max[i]) - height[i]

    return water
```

**Time:** O(n) | **Space:** O(n)

**Approach 2: Two Pointers (O(1) Space)**
```python
def trap(height: list[int]) -> int:
    left, right = 0, len(height) - 1
    left_max, right_max = height[left], height[right]
    water = 0

    while left < right:
        if left_max <= right_max:
            left += 1
            left_max = max(left_max, height[left])
            water += left_max - height[left]
        else:
            right -= 1
            right_max = max(right_max, height[right])
            water += right_max - height[right]

    return water
```

**Time:** O(n) | **Space:** O(1)

### Interview Traps

**Trap 1 — Confusing with Container With Most Water**
These look similar but are fundamentally different problems. Trapping Rain Water fills valleys; Container finds the max two-wall container.  
✅ State the distinction clearly at the start.

**Trap 2 — Not understanding why the two-pointer approach works**
The key insight: if `left_max <= right_max`, we know the water at `left` is bounded by `left_max` (the right side is at least as tall, so it won't be the limiting factor).  
✅ Explain this logic explicitly when asked to justify the approach.

**Trap 3 — Negative water (when bar is taller than max)**
`min(left_max, right_max) - height[i]` is always ≥ 0 because `left_max` and `right_max` are at least as tall as `height[i]` by definition.  
✅ No need for `max(0, ...)` guard, but mentioning it shows awareness.

**Trap 4 — Stack-based approach**
There's a third approach using a monotonic stack — useful for computing per-layer water. Know it exists, but the two-pointer approach is cleaner and preferred.

**Trap 5 — Edge cases: empty array or single bar**
`trap([])` and `trap([5])` should return 0. With `left == right` as the loop exit condition, single or zero elements return 0 correctly.

---

*[← Back to Index](./index.md) | [Next: Sliding Window →](./03_sliding_window.md)*

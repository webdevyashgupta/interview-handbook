# Math & Dynamic Programming — Extra Problems

**Grind 169 Supplement | Not in NeetCode 150**  
[← Back to Index](./index.md)

---

## Problems in this Section

| # | Problem | Difficulty |
|---|---------|------------|
| 1 | [Sort Colors](#1-sort-colors) | 🟡 Medium |
| 2 | [Maximal Square](#2-maximal-square) | 🟡 Medium |
| 3 | [Combination Sum IV](#3-combination-sum-iv) | 🟡 Medium |

---

## 1. Sort Colors

**LeetCode #75 | Difficulty: 🟡 Medium**

### Problem Statement

Given an array `nums` with values `0`, `1`, and `2` (representing red, white, blue), sort them in-place so all 0s come first, then 1s, then 2s. Must solve in one pass with O(1) space.

```
Input:  nums = [2,0,2,1,1,0]
Output: [0,0,1,1,2,2]

Input:  nums = [2,0,1]
Output: [0,1,2]
```

### Intuition

**Dutch National Flag algorithm** (Dijkstra). Maintain three pointers:
- `low`: everything before `low` is 0.
- `mid`: current element being examined.
- `high`: everything after `high` is 2.

Walk `mid` from left to right:
- `nums[mid] == 0` → swap with `low`, advance both `low` and `mid`.
- `nums[mid] == 1` → it's in the right place, advance `mid`.
- `nums[mid] == 2` → swap with `high`, decrement `high` only (don't advance `mid` — the swapped element needs to be checked).

### Solution

```python
def sortColors(nums: list[int]) -> None:
    low, mid, high = 0, 0, len(nums) - 1

    while mid <= high:
        if nums[mid] == 0:
            nums[low], nums[mid] = nums[mid], nums[low]
            low += 1
            mid += 1
        elif nums[mid] == 1:
            mid += 1
        else:  # nums[mid] == 2
            nums[mid], nums[high] = nums[high], nums[mid]
            high -= 1
            # Don't advance mid — re-examine the swapped element
```

**Time:** O(n) | **Space:** O(1)

### Interview Traps

**Trap 1 — Two-pass approach (counting sort)**
Count 0s, 1s, 2s in one pass, fill in a second pass. Valid but uses two passes.  
✅ The Dutch National Flag achieves it in one pass.

**Trap 2 — Advancing mid after swapping with high**
When swapping `nums[mid]` with `nums[high]`, the element now at `mid` came from the unexamined right portion — it could be 0, 1, or 2. Don't advance `mid` until you've examined it.

**Trap 3 — Using `sort()` as the final answer**
`nums.sort()` is O(n log n). The interviewer expects the O(n) one-pass approach.

**Trap 4 — "Generalize to k colors"**
For k colors, use counting sort O(n + k) or a multi-pivot partition. The Dutch Flag only works cleanly for 3 values.

---

## 2. Maximal Square

**LeetCode #221 | Difficulty: 🟡 Medium**

### Problem Statement

Given an `m x n` binary matrix filled with `0`s and `1`s, find the largest square containing only `1`s and return its area.

```
Input:  matrix = [["1","0","1","0","0"],
                  ["1","0","1","1","1"],
                  ["1","1","1","1","1"],
                  ["1","0","0","1","0"]]
Output: 4  (2×2 square)
```

### Intuition

`dp[i][j]` = side length of the largest square whose **bottom-right corner** is at `(i, j)`.

If `matrix[i][j] == '1'`:
```
dp[i][j] = min(dp[i-1][j], dp[i][j-1], dp[i-1][j-1]) + 1
```

The limiting factor of a square at `(i, j)` is the smallest square among its top, left, and top-left neighbors.

If `matrix[i][j] == '0'`: `dp[i][j] = 0`.

Track the maximum `dp[i][j]` seen; return `max_side²`.

### Solution

```python
def maximalSquare(matrix: list[list[str]]) -> int:
    if not matrix:
        return 0

    rows, cols = len(matrix), len(matrix[0])
    dp = [[0] * (cols + 1) for _ in range(rows + 1)]
    max_side = 0

    for i in range(1, rows + 1):
        for j in range(1, cols + 1):
            if matrix[i-1][j-1] == '1':
                dp[i][j] = min(dp[i-1][j], dp[i][j-1], dp[i-1][j-1]) + 1
                max_side = max(max_side, dp[i][j])

    return max_side * max_side
```

**Space-optimized (O(n)):**
```python
def maximalSquare(matrix: list[list[str]]) -> int:
    cols = len(matrix[0])
    dp = [0] * (cols + 1)
    max_side = prev = 0

    for row in matrix:
        new_dp = [0] * (cols + 1)
        for j in range(1, cols + 1):
            prev = dp[j-1]  # save before overwrite (top-left diagonal)
            if row[j-1] == '1':
                new_dp[j] = min(dp[j], dp[j-1], new_dp[j-1]) + 1
                # Wait — need to track diagonal separately
                max_side = max(max_side, new_dp[j])
        dp = new_dp

    return max_side * max_side
```

**Time:** O(m·n) | **Space:** O(m·n), optimizable to O(n)

### Interview Traps

**Trap 1 — Not understanding the DP recurrence**
The `min` of three neighbors is the bottleneck. Think of it as: the largest square ending at `(i,j)` is limited by the smallest of the squares ending at its top, left, and diagonal neighbors.

**Trap 2 — Returning side length instead of area**
Return `max_side * max_side` (area), not `max_side`.

**Trap 3 — Confusing with Maximal Rectangle (LC #85)**
Maximal Rectangle finds the largest rectangle of 1s (any aspect ratio) using a histogram stack approach. Maximal Square is simpler — restricted to squares only.

**Trap 4 — Matrix values are strings, not integers**
`matrix[i][j] == '1'`, not `== 1`. The input uses string characters.

**Trap 5 — Index offset when using padded dp array**
With `dp` of size `(rows+1) × (cols+1)`, access `matrix[i-1][j-1]` when filling `dp[i][j]`.

---

## 3. Combination Sum IV

**LeetCode #377 | Difficulty: 🟡 Medium**

### Problem Statement

Given an array of **distinct** integers `nums` and a target integer `target`, return the number of possible combinations that add up to `target`. Order matters — different orderings count as different combinations.

```
Input:  nums = [1,2,3], target = 4
Output: 7
([1,1,1,1], [1,1,2], [1,2,1], [1,3], [2,1,1], [2,2], [3,1])
```

### Intuition

This is **unbounded knapsack where order matters** — essentially counting permutations, not combinations. 

`dp[i]` = number of ways to make sum `i`. For each amount, try all numbers:
```
dp[i] = sum(dp[i - num] for num in nums if num <= i)
```

Key distinction from Coin Change II: here the **outer loop is over amounts, inner loop over nums** — this counts ordered sequences (permutations). Coin Change II does it reversed to count unordered combinations.

### Solution

```python
def combinationSum4(nums: list[int], target: int) -> int:
    dp = [0] * (target + 1)
    dp[0] = 1  # one way to make 0: use nothing

    for amount in range(1, target + 1):
        for num in nums:
            if num <= amount:
                dp[amount] += dp[amount - num]

    return dp[target]
```

**Time:** O(target · len(nums)) | **Space:** O(target)

### Interview Traps

**Trap 1 — Confusing with Combination Sum I/II**
Combination Sum I & II return the actual combinations (unordered). Combination Sum IV counts ordered sequences. `[1,2]` and `[2,1]` are the same in I/II but different here.

**Trap 2 — Using the Coin Change II approach (wrong loop order)**
Coin Change II: outer loop = nums, inner loop = amounts → counts unordered combinations.  
Combination Sum IV: outer loop = amounts, inner loop = nums → counts ordered sequences.  
✅ Loop order is the entire difference.

**Trap 3 — Initializing dp[0] = 1**
There is exactly 1 way to make sum 0: use no numbers. This is the critical base case — without it, nothing propagates.

**Trap 4 — "What if negative numbers are allowed?"**
With negative numbers, there could be infinite combinations (e.g., `[1, -1]` can cycle forever). The problem guarantees positive integers only.

**Trap 5 — Integer overflow for large targets**
The count can grow exponentially. Python handles big integers natively; in Java/C++ use `long` and add overflow guards.

---

*[← Back to Index](./index.md)*

---

## 🎉 Grind 169 Supplement Complete!

You've now covered every problem in Grind 169 that wasn't in NeetCode 150.

### Full Coverage Summary

| Handbook | Problems Covered |
|----------|-----------------|
| NeetCode 150 Handbook | 150 problems |
| Grind 169 Supplement (this handbook) | ~47 extra problems |
| **Combined total** | **~197 unique problems** |

### What this covers
- ✅ All of **Blind 75** (full subset of NeetCode 150)
- ✅ All of **Grind 75** (NeetCode 150 + this supplement)
- ✅ All of **Grind 169** (NeetCode 150 + this supplement)
- ✅ All of **NeetCode 150**

**Good luck with your interviews! 🚀**

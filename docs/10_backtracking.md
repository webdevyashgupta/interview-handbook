# Backtracking

**Topic 10 of 18 | NeetCode 150 Interview Handbook**  
[← Back to Index](./index.md)

---

## Problems in this Section

| # | Problem | Difficulty |
|---|---------|------------|
| 71 | [Subsets](#71-subsets) | 🟡 Medium |
| 72 | [Combination Sum](#72-combination-sum) | 🟡 Medium |
| 73 | [Permutations](#73-permutations) | 🟡 Medium |
| 74 | [Subsets II](#74-subsets-ii) | 🟡 Medium |
| 75 | [Combination Sum II](#75-combination-sum-ii) | 🟡 Medium |
| 76 | [Word Search](#76-word-search) | 🟡 Medium |
| 77 | [Palindrome Partitioning](#77-palindrome-partitioning) | 🟡 Medium |
| 78 | [Letter Combinations of a Phone Number](#78-letter-combinations-of-a-phone-number) | 🟡 Medium |
| 79 | [N-Queens](#79-n-queens) | 🔴 Hard |

---

## 71. Subsets

**LeetCode #78 | Difficulty: 🟡 Medium**

### Problem Statement

Given an integer array `nums` of unique elements, return all possible subsets (the power set).

```
Input:  nums = [1,2,3]
Output: [[],[1],[2],[1,2],[3],[1,3],[2,3],[1,2,3]]
```

### Intuition

At each index, make a binary choice: **include** or **exclude** the current element. Recurse with the next index until all elements are considered. Each leaf of the recursion tree is a valid subset.

### Solution

```python
def subsets(nums: list[int]) -> list[list[int]]:
    result = []

    def backtrack(start, current):
        result.append(list(current))  # every state is a valid subset
        for i in range(start, len(nums)):
            current.append(nums[i])
            backtrack(i + 1, current)
            current.pop()

    backtrack(0, [])
    return result
```

**Iterative approach:**
```python
def subsets(nums: list[int]) -> list[list[int]]:
    result = [[]]
    for num in nums:
        result += [subset + [num] for subset in result]
    return result
```

**Time:** O(2^n · n) | **Space:** O(n) recursion depth

### Interview Traps

**Trap 1 — Appending the reference instead of a copy**
```python
# ❌ Appends the same list object — all entries will be identical
result.append(current)

# ✅ Append a copy
result.append(list(current))
```

**Trap 2 — Adding result only at the leaf**
Every node in the recursion tree is a valid subset, not just the leaves.  
✅ Append at the start of `backtrack`, before the loop.

**Trap 3 — "How many subsets exist?"**
For n elements, there are 2^n subsets (each element is in or out). Knowing this bounds the output.

**Trap 4 — Iterative vs recursive**
The iterative approach builds subsets by doubling the list at each step. Elegant and worth knowing.

---

## 72. Combination Sum

**LeetCode #39 | Difficulty: 🟡 Medium**

### Problem Statement

Given an array of distinct integers `candidates` and a `target`, return all unique combinations where the numbers sum to `target`. Each number may be used **unlimited** times.

```
Input:  candidates = [2,3,6,7], target = 7
Output: [[2,2,3],[7]]
```

### Intuition

Backtracking: at each step, try each candidate from the current index forward (allowing reuse). If the remaining target becomes 0 → valid combination. If it goes negative → prune.

Sort candidates first to enable early termination.

### Solution

```python
def combinationSum(candidates: list[int], target: int) -> list[list[int]]:
    candidates.sort()
    result = []

    def backtrack(start, current, remaining):
        if remaining == 0:
            result.append(list(current))
            return
        for i in range(start, len(candidates)):
            if candidates[i] > remaining:
                break  # sorted: no point continuing
            current.append(candidates[i])
            backtrack(i, current, remaining - candidates[i])  # i, not i+1 (reuse allowed)
            current.pop()

    backtrack(0, [], target)
    return result
```

**Time:** O(2^(t/m)) where t=target, m=min candidate | **Space:** O(t/m) depth

### Interview Traps

**Trap 1 — Not allowing reuse (using i+1 instead of i)**
The problem says each number can be used unlimited times. Pass `i` (not `i+1`) in the recursive call.

**Trap 2 — Not pruning when candidate exceeds remaining**
Sorting + `break` prunes the search early — important for performance.

**Trap 3 — Duplicate combinations**
Starting from `start` (not 0) ensures we never go backward — prevents `[2,2,3]` and `[2,3,2]` from both appearing.

**Trap 4 — "What if candidates can be reused at most twice?"**
Track a count map and pass it through the recursion.

---

## 73. Permutations

**LeetCode #46 | Difficulty: 🟡 Medium**

### Problem Statement

Given an array `nums` of distinct integers, return all possible permutations.

```
Input:  nums = [1,2,3]
Output: [[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]
```

### Intuition

At each step, choose any unused element. Mark it as used, recurse, then unmark (backtrack). When the current permutation has n elements, it's complete.

### Solution

```python
def permute(nums: list[int]) -> list[list[int]]:
    result = []

    def backtrack(current, remaining):
        if not remaining:
            result.append(list(current))
            return
        for i in range(len(remaining)):
            current.append(remaining[i])
            backtrack(current, remaining[:i] + remaining[i+1:])
            current.pop()

    backtrack([], nums)
    return result
```

**In-place swap approach (more efficient):**
```python
def permute(nums: list[int]) -> list[list[int]]:
    result = []

    def backtrack(start):
        if start == len(nums):
            result.append(list(nums))
            return
        for i in range(start, len(nums)):
            nums[start], nums[i] = nums[i], nums[start]
            backtrack(start + 1)
            nums[start], nums[i] = nums[i], nums[start]  # restore

    backtrack(0)
    return result
```

**Time:** O(n! · n) | **Space:** O(n)

### Interview Traps

**Trap 1 — Not creating a copy on result append**
Always `result.append(list(nums))` — not `result.append(nums)`.

**Trap 2 — Slicing vs visited array**
Slicing creates O(n) copies per call. A `visited` boolean array or in-place swapping is more efficient.

**Trap 3 — "What if there are duplicates?" (LeetCode #47)**
Sort first, then skip duplicates at each level: `if i > start and nums[i] == nums[i-1]: continue`.

**Trap 4 — Counting permutations**
n! permutations exist for n distinct elements. Verify: `len(permute([1,2,3]))` = 6 = 3!.

---

## 74. Subsets II

**LeetCode #90 | Difficulty: 🟡 Medium**

### Problem Statement

Given an integer array `nums` that may contain duplicates, return all possible subsets without duplicate subsets.

```
Input:  nums = [1,2,2]
Output: [[],[1],[1,2],[1,2,2],[2],[2,2]]
```

### Intuition

Sort first. During backtracking, **skip duplicates at the same level** — if `nums[i] == nums[i-1]` and `i > start`, we'd be making the same choice as before at this level, producing a duplicate subset.

### Solution

```python
def subsetsWithDup(nums: list[int]) -> list[list[int]]:
    nums.sort()
    result = []

    def backtrack(start, current):
        result.append(list(current))
        for i in range(start, len(nums)):
            if i > start and nums[i] == nums[i-1]:
                continue  # skip duplicate at same level
            current.append(nums[i])
            backtrack(i + 1, current)
            current.pop()

    backtrack(0, [])
    return result
```

**Time:** O(2^n · n) | **Space:** O(n)

### Interview Traps

**Trap 1 — Not sorting first**
Duplicate detection relies on adjacent equal elements — only works after sorting.

**Trap 2 — Wrong duplicate condition: `i > 0` instead of `i > start`**
```python
# ❌ Wrong: skips valid elements from different parent paths
if i > 0 and nums[i] == nums[i-1]: continue

# ✅ Correct: only skip duplicates at the SAME recursion level
if i > start and nums[i] == nums[i-1]: continue
```

**Trap 3 — Confusing with Subsets I (no duplicates)**
The only addition to Subsets I is the sort + duplicate skip. Understand the difference clearly.

---

## 75. Combination Sum II

**LeetCode #40 | Difficulty: 🟡 Medium**

### Problem Statement

Given a collection `candidates` (may have duplicates) and a `target`, return all unique combinations that sum to target. Each number may only be used **once**.

```
Input:  candidates = [10,1,2,7,6,1,5], target = 8
Output: [[1,1,6],[1,2,5],[1,7],[2,6]]
```

### Intuition

Sort first. Backtrack as in Combination Sum, but:
1. Move to `i+1` (no reuse).
2. Skip duplicates at the same level (`i > start and candidates[i] == candidates[i-1]`).

### Solution

```python
def combinationSum2(candidates: list[int], target: int) -> list[list[int]]:
    candidates.sort()
    result = []

    def backtrack(start, current, remaining):
        if remaining == 0:
            result.append(list(current))
            return
        for i in range(start, len(candidates)):
            if i > start and candidates[i] == candidates[i-1]:
                continue  # skip duplicates at same level
            if candidates[i] > remaining:
                break
            current.append(candidates[i])
            backtrack(i + 1, current, remaining - candidates[i])
            current.pop()

    backtrack(0, [], target)
    return result
```

**Time:** O(2^n · n) | **Space:** O(n)

### Interview Traps

**Trap 1 — Confusing with Combination Sum I**
Combination Sum I: unlimited reuse, no duplicates in candidates.  
Combination Sum II: single use, duplicates in candidates.  
✅ The key differences: `i+1` in recursion (not `i`) and the duplicate-skip guard.

**Trap 2 — Wrong duplicate skip condition**
Same as Subsets II — must be `i > start`, not `i > 0`.

**Trap 3 — Not sorting**
Without sorting, adjacent-duplicate detection doesn't work, and `break` early termination doesn't apply.

---

## 76. Word Search

**LeetCode #79 | Difficulty: 🟡 Medium**

### Problem Statement

Given an `m x n` grid of characters `board` and a string `word`, return `true` if the word exists in the grid. The word must be constructed from adjacent cells (horizontally or vertically), and each cell may not be used more than once.

```
Input:  board = [["A","B","C","E"],["S","F","C","S"],["A","D","E","E"]], word = "ABCCED"
Output: true
```

### Intuition

DFS backtracking from every cell. At each step, check if the current cell matches the next expected character. Mark the cell as visited (temporarily), recurse in 4 directions, then restore the cell.

### Solution

```python
def exist(board: list[list[str]], word: str) -> bool:
    rows, cols = len(board), len(board[0])

    def dfs(r, c, i):
        if i == len(word):
            return True
        if r < 0 or r >= rows or c < 0 or c >= cols:
            return False
        if board[r][c] != word[i]:
            return False
        if board[r][c] == '#':
            return False

        temp = board[r][c]
        board[r][c] = '#'  # mark visited
        found = (dfs(r+1, c, i+1) or dfs(r-1, c, i+1) or
                 dfs(r, c+1, i+1) or dfs(r, c-1, i+1))
        board[r][c] = temp  # restore

        return found

    for r in range(rows):
        for c in range(cols):
            if dfs(r, c, 0):
                return True
    return False
```

**Time:** O(m·n·4^L) where L = len(word) | **Space:** O(L)

### Interview Traps

**Trap 1 — Using a visited set instead of in-place marking**
A separate set uses O(m·n) extra space. In-place marking with a sentinel is O(1) extra space.

**Trap 2 — Not restoring the cell after backtracking**
The board is shared across recursive calls — always restore `board[r][c]` before returning.

**Trap 3 — Checking bounds after accessing board**
Check bounds before accessing — `r < 0 or r >= rows or c < 0 or c >= cols` must come before `board[r][c]`.

**Trap 4 — "Can the same cell be used in different DFS starts?"**
Yes — each DFS call from a new starting cell gets a fresh "visited" state. The `'#'` marker is restored after each DFS branch.

---

## 77. Palindrome Partitioning

**LeetCode #131 | Difficulty: 🟡 Medium**

### Problem Statement

Given a string `s`, partition it so that every substring of the partition is a palindrome. Return all possible palindrome partitioning.

```
Input:  s = "aab"
Output: [["a","a","b"],["aa","b"]]
```

### Intuition

Backtracking: at each position, try every possible prefix. If the prefix is a palindrome, recurse on the remaining string. When the entire string is consumed, save the partition.

Pre-compute palindrome checks using DP for efficiency.

### Solution

```python
def partition(s: str) -> list[list[str]]:
    result = []
    n = len(s)

    # DP: is_palindrome[i][j] = True if s[i..j] is palindrome
    dp = [[False] * n for _ in range(n)]
    for i in range(n - 1, -1, -1):
        for j in range(i, n):
            if s[i] == s[j] and (j - i <= 2 or dp[i+1][j-1]):
                dp[i][j] = True

    def backtrack(start, current):
        if start == n:
            result.append(list(current))
            return
        for end in range(start, n):
            if dp[start][end]:
                current.append(s[start:end+1])
                backtrack(end + 1, current)
                current.pop()

    backtrack(0, [])
    return result
```

**Time:** O(n · 2^n) | **Space:** O(n²) for DP table

### Interview Traps

**Trap 1 — Checking palindrome naively in backtracking**
An O(n) palindrome check per substring leads to O(n² · 2^n) total.  
✅ Pre-compute with DP for O(1) lookup.

**Trap 2 — Palindrome DP base cases**
- Single char (`j == i`): always palindrome.
- Two chars (`j == i+1`): palindrome if `s[i] == s[j]`.
- Three+ chars: palindrome if outer chars match AND inner substring is palindrome.

**Trap 3 — Not copying current list on append**
`result.append(list(current))` not `result.append(current)`.

---

## 78. Letter Combinations of a Phone Number

**LeetCode #17 | Difficulty: 🟡 Medium**

### Problem Statement

Given a string of digits (2-9), return all possible letter combinations using a phone keypad mapping.

```
Input:  digits = "23"
Output: ["ad","ae","af","bd","be","bf","cd","ce","cf"]
```

### Intuition

Backtracking: for each digit, try each mapped letter. Recurse for the next digit. When all digits are processed, save the combination.

### Solution

```python
def letterCombinations(digits: str) -> list[str]:
    if not digits:
        return []

    phone = {
        '2': 'abc', '3': 'def', '4': 'ghi', '5': 'jkl',
        '6': 'mno', '7': 'pqrs', '8': 'tuv', '9': 'wxyz'
    }
    result = []

    def backtrack(i, current):
        if i == len(digits):
            result.append(current)
            return
        for char in phone[digits[i]]:
            backtrack(i + 1, current + char)

    backtrack(0, '')
    return result
```

**Time:** O(4^n · n) | **Space:** O(n)

### Interview Traps

**Trap 1 — Not handling empty input**
`letterCombinations("")` should return `[]`, not `[""]`.  
✅ Early return `if not digits: return []`.

**Trap 2 — Hardcoding the keypad**
Define a clean mapping dict — hardcoding conditionals is error-prone and inextensible.

**Trap 3 — "What about 0 and 1?"**
Standard phone keypad doesn't map 0 or 1 to letters. Clarify with interviewer whether to handle them.

**Trap 4 — Iterative approach**
```python
result = ['']
for digit in digits:
    result = [prev + char for prev in result for char in phone[digit]]
return result
```
Clean iterative alternative — worth knowing.

---

## 79. N-Queens

**LeetCode #51 | Difficulty: 🔴 Hard**

### Problem Statement

Place `n` queens on an `n×n` chessboard such that no two queens attack each other (no two queens share the same row, column, or diagonal). Return all distinct solutions.

```
Input:  n = 4
Output: [[".Q..","...Q","Q...","..Q."],["..Q.","Q...","...Q",".Q.."]]
```

### Intuition

Place queens one row at a time. For each row, try each column. A placement is valid if no existing queen shares the column or either diagonal. Track:
- `cols`: set of used columns
- `pos_diag`: set of used `(r - c)` values (positive diagonal)
- `neg_diag`: set of used `(r + c)` values (negative diagonal)

### Solution

```python
def solveNQueens(n: int) -> list[list[str]]:
    cols = set()
    pos_diag = set()  # r - c
    neg_diag = set()  # r + c
    board = [['.'] * n for _ in range(n)]
    result = []

    def backtrack(r):
        if r == n:
            result.append([''.join(row) for row in board])
            return
        for c in range(n):
            if c in cols or (r - c) in pos_diag or (r + c) in neg_diag:
                continue
            cols.add(c)
            pos_diag.add(r - c)
            neg_diag.add(r + c)
            board[r][c] = 'Q'

            backtrack(r + 1)

            cols.remove(c)
            pos_diag.remove(r - c)
            neg_diag.remove(r + c)
            board[r][c] = '.'

    backtrack(0)
    return result
```

**Time:** O(n!) | **Space:** O(n²)

### Interview Traps

**Trap 1 — Checking conflicts by scanning the board**
Scanning all previous queens for conflicts is O(n) per placement → O(n²) per level. Using sets reduces this to O(1).

**Trap 2 — Diagonal identification**
- Same positive diagonal: `r - c` is constant (going top-left to bottom-right).
- Same negative diagonal: `r + c` is constant (going top-right to bottom-left).

**Trap 3 — Forgetting to restore state**
Always remove from all three sets and reset `board[r][c] = '.'` after recursing.

**Trap 4 — "Return just the count" (LeetCode #52)**
Replace `result.append(...)` with `result[0] += 1` (or use a counter). The backtracking structure is identical.

**Trap 5 — "Why place queens row by row?"**
Each row must have exactly one queen. Processing row by row eliminates row conflicts trivially and reduces the branching factor to n (columns) per level.

---

*[← Back to Index](./index.md) | [Next: Graphs →](./11_graphs.md)*

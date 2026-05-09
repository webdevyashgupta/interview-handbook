# Math & Geometry

**Topic 17 of 18 | NeetCode 150 Interview Handbook**  
[← Back to Index](./index.md)

---

## Problems in this Section

| # | Problem | Difficulty |
|---|---------|------------|
| 136 | [Rotate Image](#136-rotate-image) | 🟡 Medium |
| 137 | [Spiral Matrix](#137-spiral-matrix) | 🟡 Medium |
| 138 | [Set Matrix Zeroes](#138-set-matrix-zeroes) | 🟡 Medium |
| 139 | [Happy Number](#139-happy-number) | 🟢 Easy |
| 140 | [Plus One](#140-plus-one) | 🟢 Easy |
| 141 | [Pow(x, n)](#141-powx-n) | 🟡 Medium |
| 142 | [Multiply Strings](#142-multiply-strings) | 🟡 Medium |
| 143 | [Detect Squares](#143-detect-squares) | 🟡 Medium |

---

## 136. Rotate Image

**LeetCode #48 | Difficulty: 🟡 Medium**

### Problem Statement

Given an `n x n` 2D matrix representing an image, rotate it 90 degrees clockwise **in-place**.

```
Input:  [[1,2,3],[4,5,6],[7,8,9]]
Output: [[7,4,1],[8,5,2],[9,6,3]]
```

### Intuition

Rotating 90° clockwise = **Transpose** + **Reverse each row**.

- Transpose: `matrix[i][j] ↔ matrix[j][i]`
- Reverse each row: `matrix[i].reverse()`

Alternatively: **Reverse each column** (flip vertically) + **Transpose**.

### Solution

```python
def rotate(matrix: list[list[int]]) -> None:
    n = len(matrix)

    # Step 1: Transpose
    for i in range(n):
        for j in range(i + 1, n):
            matrix[i][j], matrix[j][i] = matrix[j][i], matrix[i][j]

    # Step 2: Reverse each row
    for row in matrix:
        row.reverse()
```

**Time:** O(n²) | **Space:** O(1)

### Interview Traps

**Trap 1 — Transposing with the wrong loop bounds**
Only swap `(i, j)` where `j > i` — otherwise you swap twice and undo the transpose.  
✅ Inner loop starts at `j = i + 1`.

**Trap 2 — Counterclockwise rotation**
Counterclockwise = Transpose + Reverse each column (or Reverse rows + Transpose).

**Trap 3 — Creating a new matrix**
The problem requires in-place rotation. Using a copy is O(n²) space — mention it but implement the O(1) version.

**Trap 4 — Not understanding the math**
Clockwise rotation maps `(i, j)` to `(j, n-1-i)`. You can directly compute new positions using four-way swaps on the boundary rings.

---

## 137. Spiral Matrix

**LeetCode #54 | Difficulty: 🟡 Medium**

### Problem Statement

Given an `m x n` matrix, return all elements in spiral order (clockwise from top-left).

```
Input:  [[1,2,3],[4,5,6],[7,8,9]]
Output: [1,2,3,6,9,8,7,4,5]
```

### Intuition

Maintain four boundaries: `top`, `bottom`, `left`, `right`. In each iteration:
1. Traverse right across the top row, then shrink `top`.
2. Traverse down the right column, then shrink `right`.
3. Traverse left across the bottom row (if valid), then shrink `bottom`.
4. Traverse up the left column (if valid), then shrink `left`.

Repeat until boundaries cross.

### Solution

```python
def spiralOrder(matrix: list[list[int]]) -> list[int]:
    result = []
    top, bottom = 0, len(matrix) - 1
    left, right = 0, len(matrix[0]) - 1

    while top <= bottom and left <= right:
        # Traverse right
        for c in range(left, right + 1):
            result.append(matrix[top][c])
        top += 1

        # Traverse down
        for r in range(top, bottom + 1):
            result.append(matrix[r][right])
        right -= 1

        # Traverse left (guard for single row)
        if top <= bottom:
            for c in range(right, left - 1, -1):
                result.append(matrix[bottom][c])
            bottom -= 1

        # Traverse up (guard for single column)
        if left <= right:
            for r in range(bottom, top - 1, -1):
                result.append(matrix[r][left])
            left += 1

    return result
```

**Time:** O(m·n) | **Space:** O(1) excluding output

### Interview Traps

**Trap 1 — Not guarding left/bottom traversal**
For a single-row or single-column matrix, the left and up traversals would double-count. Always check `top <= bottom` and `left <= right`.

**Trap 2 — Off-by-one in range bounds**
Right traversal: `range(left, right + 1)`. Left traversal: `range(right, left - 1, -1)`.

**Trap 3 — "Spiral Matrix II" (generate a spiral)**
Fill a matrix with numbers 1 to n² in spiral order. Same boundary logic, but assign values instead of reading them.

---

## 138. Set Matrix Zeroes

**LeetCode #73 | Difficulty: 🟡 Medium**

### Problem Statement

Given an `m x n` matrix, if any element is 0, set its entire row and column to 0. Do it in-place.

```
Input:  [[1,1,1],[1,0,1],[1,1,1]]
Output: [[1,0,1],[0,0,0],[1,0,1]]
```

### Intuition

**Naive approach:** Use two sets to track zero rows and columns, then zero them out. O(m+n) extra space.

**O(1) space approach:** Use the first row and first column as markers. Track separately whether the first row/column themselves contain zeros.

### Solution

```python
def setZeroes(matrix: list[list[int]]) -> None:
    rows, cols = len(matrix), len(matrix[0])
    first_row_zero = any(matrix[0][c] == 0 for c in range(cols))
    first_col_zero = any(matrix[r][0] == 0 for r in range(rows))

    # Mark zeros in first row/col
    for r in range(1, rows):
        for c in range(1, cols):
            if matrix[r][c] == 0:
                matrix[r][0] = 0
                matrix[0][c] = 0

    # Zero out cells using markers
    for r in range(1, rows):
        for c in range(1, cols):
            if matrix[r][0] == 0 or matrix[0][c] == 0:
                matrix[r][c] = 0

    # Handle first row/col
    if first_row_zero:
        for c in range(cols):
            matrix[0][c] = 0
    if first_col_zero:
        for r in range(rows):
            matrix[r][0] = 0
```

**Time:** O(m·n) | **Space:** O(1)

### Interview Traps

**Trap 1 — Zeroing cells immediately while scanning**
If you zero cells as you encounter them, you'll incorrectly mark rows/columns that didn't originally have zeros.  
✅ Always mark first, zero out second.

**Trap 2 — Using the first row/column as markers without checking them first**
The first row/column might themselves contain zeros. Record this before using them as markers.

**Trap 3 — Processing first row/column markers last**
Zero out the interior first (rows 1..m, cols 1..n), then handle the first row and column separately.

---

## 139. Happy Number

**LeetCode #202 | Difficulty: 🟢 Easy**

### Problem Statement

A happy number is defined by the process: replace the number by the sum of squares of its digits, repeat until it equals 1 (happy) or loops forever (not happy). Return `true` if `n` is happy.

```
Input:  n = 19
Output: true  (1²+9²=82→68→100→1)

Input:  n = 2
Output: false
```

### Intuition

The process either reaches 1 or enters a cycle. Detect cycles using **Floyd's Cycle Detection** (fast and slow pointers) or a **HashSet** of seen numbers.

### Solution

```python
def isHappy(n: int) -> bool:
    def sum_squares(num):
        total = 0
        while num:
            digit = num % 10
            total += digit * digit
            num //= 10
        return total

    slow, fast = n, sum_squares(n)
    while fast != 1 and slow != fast:
        slow = sum_squares(slow)
        fast = sum_squares(sum_squares(fast))

    return fast == 1
```

**HashSet version:**
```python
def isHappy(n: int) -> bool:
    seen = set()
    while n != 1 and n not in seen:
        seen.add(n)
        n = sum(int(d)**2 for d in str(n))
    return n == 1
```

**Time:** O(log n) per step | **Space:** O(1) Floyd's, O(n) HashSet

### Interview Traps

**Trap 1 — Not detecting the cycle**
Without cycle detection, the loop runs forever for unhappy numbers.

**Trap 2 — Floyd's vs HashSet trade-off**
HashSet is simpler to understand; Floyd's is O(1) space. Know both.

**Trap 3 — Computing digit squares**
Use modulo and integer division for digit extraction — faster than converting to string.

---

## 140. Plus One

**LeetCode #66 | Difficulty: 🟢 Easy**

### Problem Statement

Given a non-empty array of digits representing a non-negative integer, increment the integer by one and return the result as an array.

```
Input:  digits = [1,2,3]
Output: [1,2,4]

Input:  digits = [9,9,9]
Output: [1,0,0,0]
```

### Intuition

Simulate the addition from right to left. If the digit is < 9, increment and return. If it's 9, set to 0 and carry. If we exit the loop with a carry, prepend a 1.

### Solution

```python
def plusOne(digits: list[int]) -> list[int]:
    for i in range(len(digits) - 1, -1, -1):
        if digits[i] < 9:
            digits[i] += 1
            return digits
        digits[i] = 0

    return [1] + digits  # All 9s case: [9,9,9] → [1,0,0,0]
```

**Time:** O(n) | **Space:** O(1) usually, O(n) for all-9s

### Interview Traps

**Trap 1 — Not handling the all-9s case**
`[9,9,9]` becomes `[1,0,0,0]` — the result has one more digit. Prepend `1` after zeroing all digits.

**Trap 2 — Using integer conversion**
`int(''.join(map(str, digits))) + 1` works but defeats the purpose of the array representation. Implement the carry logic directly.

**Trap 3 — Modifying in place vs creating new**
Modifying in place is preferred unless the all-9s case occurs (which requires a new array anyway).

---

## 141. Pow(x, n)

**LeetCode #50 | Difficulty: 🟡 Medium**

### Problem Statement

Implement `pow(x, n)` — x raised to the power n. `n` can be negative.

```
Input:  x = 2.00000, n = 10
Output: 1024.00000

Input:  x = 2.00000, n = -2
Output: 0.25000
```

### Intuition

**Fast exponentiation (exponentiation by squaring):** Recursively compute `pow(x, n//2)` and square it. If `n` is odd, multiply by `x` once more. This halves the exponent each step → O(log n).

Handle negative `n` by inverting: `pow(x, -n) = 1 / pow(x, n)`.

### Solution

```python
def myPow(x: float, n: int) -> float:
    def fast_pow(base, exp):
        if exp == 0:
            return 1
        half = fast_pow(base, exp // 2)
        if exp % 2 == 0:
            return half * half
        else:
            return half * half * base

    if n < 0:
        x = 1 / x
        n = -n

    return fast_pow(x, n)
```

**Iterative version:**
```python
def myPow(x: float, n: int) -> float:
    if n < 0:
        x = 1 / x
        n = -n
    result = 1
    while n:
        if n % 2 == 1:
            result *= x
        x *= x
        n //= 2
    return result
```

**Time:** O(log n) | **Space:** O(log n) recursive, O(1) iterative

### Interview Traps

**Trap 1 — Linear O(n) loop**
Multiplying `x` by itself `n` times is O(n) and too slow for large n (up to 2^31).

**Trap 2 — Not handling negative n**
`pow(2, -3) = 1/8`. Invert `x` and negate `n` before calling fast_pow.

**Trap 3 — Integer overflow with -n**
In languages with fixed-size integers (Java/C++), `-n` overflows when `n = INT_MIN`. In Python this isn't an issue.

**Trap 4 — Recursive vs iterative**
Recursion is cleaner; iteration is O(1) space. Know both — interviewers may ask for the iterative version.

---

## 142. Multiply Strings

**LeetCode #43 | Difficulty: 🟡 Medium**

### Problem Statement

Given two non-negative integers `num1` and `num2` represented as strings, return their product as a string. You may not use built-in BigInteger or convert directly to integers.

```
Input:  num1 = "2", num2 = "3"
Output: "6"

Input:  num1 = "123", num2 = "456"
Output: "56088"
```

### Intuition

Simulate grade-school multiplication. The product of `num1[i]` and `num2[j]` contributes to positions `i + j` and `i + j + 1` in the result array (0-indexed from the left).

Use a result array of length `m + n`, fill it via digit-by-digit multiplication with carry, then strip leading zeros.

### Solution

```python
def multiply(num1: str, num2: str) -> str:
    m, n = len(num1), len(num2)
    pos = [0] * (m + n)

    for i in range(m - 1, -1, -1):
        for j in range(n - 1, -1, -1):
            mul = (ord(num1[i]) - ord('0')) * (ord(num2[j]) - ord('0'))
            p1, p2 = i + j, i + j + 1
            total = mul + pos[p2]
            pos[p2] = total % 10
            pos[p1] += total // 10

    result = ''.join(str(d) for d in pos).lstrip('0')
    return result if result else '0'
```

**Time:** O(m·n) | **Space:** O(m+n)

### Interview Traps

**Trap 1 — Wrong position indexing**
`num1[i] * num2[j]` contributes to positions `i+j` (carry) and `i+j+1` (digit). Off-by-one here causes incorrect results.

**Trap 2 — Not stripping leading zeros**
`lstrip('0')` removes them. If the result is all zeros (e.g., `"0" × "5"`), return `"0"`.

**Trap 3 — Converting to int (not allowed)**
The problem explicitly forbids this. Implement digit-by-digit arithmetic.

**Trap 4 — Handling "0" inputs**
`"0" * anything = "0"`. The algorithm handles this naturally — all positions stay 0, and `lstrip('0') or '0'` returns `"0"`.

---

## 143. Detect Squares

**LeetCode #2013 | Difficulty: 🟡 Medium**

### Problem Statement

Design a data structure that adds points and counts the number of axis-aligned squares that include a given query point as one of its corners.

```
detectSquares.add([3,10]); detectSquares.add([11,2]); detectSquares.add([3,2])
detectSquares.count([11,10]) → 1
```

### Intuition

For a query point `(px, py)`, iterate over all points sharing the same x-coordinate as `px` (`(px, y)` for all y ≠ py). These form a vertical edge. The square's diagonal is `(px, y)` to `(px+d, py)` and `(px+d, y)`.

For each such vertical pair, the fourth corner must exist at `(px + d, y)` and `(px + d, py)` — check their counts in the frequency map.

### Solution

```python
from collections import defaultdict

class DetectSquares:
    def __init__(self):
        self.pt_count = defaultdict(int)   # (x,y) -> count
        self.x_to_ys = defaultdict(set)    # x -> set of y values

    def add(self, point: list[int]) -> None:
        x, y = point
        self.pt_count[(x, y)] += 1
        self.x_to_ys[x].add(y)

    def count(self, point: list[int]) -> int:
        px, py = point
        total = 0

        for y in self.x_to_ys[px]:
            if y == py:
                continue
            d = y - py  # side length (signed)
            # Two possible squares: to the right or left
            for dx in [d, -d]:
                qx = px + dx
                total += (self.pt_count[(px, y)] *
                          self.pt_count[(qx, py)] *
                          self.pt_count[(qx, y)])

        return total
```

**Time:** O(n) per count call | **Space:** O(n)

### Interview Traps

**Trap 1 — Only checking one direction for the square**
Given a vertical edge `(px, py)` to `(px, y)`, the square can extend left OR right. Check both `px + d` and `px - d`.

**Trap 2 — Not handling duplicate points**
Points can be added multiple times. Track frequencies with a count map.

**Trap 3 — Iterating over all points instead of same-x points**
For each query, only iterate over points sharing the same x as the query — the `x_to_ys` dict enables this.

**Trap 4 — Including the query point itself as a vertical edge**
Skip `y == py` — that would make a degenerate square with zero area.

---

*[← Back to Index](./index.md) | [Next: Bit Manipulation →](./18_bit_manipulation.md)*

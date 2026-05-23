# Bit Manipulation

**Topic 18 of 18 | NeetCode 150 Interview Handbook**  
[← Back to Index](./index.md)

---

## Problems in this Section

| # | Problem | Difficulty |
|---|---------|------------|
| 144 | [Single Number](#144-single-number) | 🟢 Easy |
| 145 | [Number of 1 Bits](#145-number-of-1-bits) | 🟢 Easy |
| 146 | [Counting Bits](#146-counting-bits) | 🟢 Easy |
| 147 | [Reverse Bits](#147-reverse-bits) | 🟢 Easy |
| 148 | [Missing Number](#148-missing-number) | 🟢 Easy |
| 149 | [Sum of Two Integers](#149-sum-of-two-integers) | 🟡 Medium |
| 150 | [Reverse Integer](#150-reverse-integer) | 🟡 Medium |

---

## 144. Single Number

**LeetCode #136 | Difficulty: 🟢 Easy**

### Problem Statement

Given a non-empty array of integers where every element appears **twice** except for one, find that single element. Must run in O(n) time and O(1) space.

```
Input:  nums = [2,2,1]
Output: 1

Input:  nums = [4,1,2,1,2]
Output: 4
```

### Intuition

**XOR trick**: `x XOR x = 0` and `x XOR 0 = x`. XOR-ing all elements cancels every pair, leaving only the single element.

### Solution

```python
def singleNumber(nums: list[int]) -> int:
    result = 0
    for num in nums:
        result ^= num
    return result
```

**One-liner:**
```python
from functools import reduce
import operator
return reduce(operator.xor, nums)
```

**Time:** O(n) | **Space:** O(1)

### Interview Traps

**Trap 1 — Using a HashSet (O(n) space)**
Works but violates the O(1) space constraint.  
✅ XOR satisfies both time and space requirements.

**Trap 2 — "What if one element appears 3 times and all others twice?"**
XOR alone doesn't work. Use a bitmask approach with mod 3 per bit, or maintain two variables (`ones`, `twos`).

**Trap 3 — Explaining why XOR works**
Be ready to articulate: "XOR is commutative and associative. Any number XOR'd with itself is 0, and 0 XOR x = x. So all pairs cancel and the single number remains."

**Trap 4 — "What if there are two single numbers?" (LeetCode #260)**
XOR all → get `a XOR b`. Find any set bit (differentiator), split into two groups by that bit, XOR each group separately.

---

## 145. Number of 1 Bits

**LeetCode #191 | Difficulty: 🟢 Easy**

### Problem Statement

Given an unsigned integer, return the number of `1` bits (Hamming weight).

```
Input:  n = 11 (binary: 00000000000000000000000000001011)
Output: 3

Input:  n = 128 (binary: 10000000)
Output: 1
```

### Intuition

**Method 1:** Check each bit with `n & 1`, then right-shift. O(32).

**Method 2 (Brian Kernighan's trick):** `n & (n-1)` clears the lowest set bit. Count how many times until n = 0. O(number of 1 bits).

### Solution

```python
def hammingWeight(n: int) -> int:
    count = 0
    while n:
        n &= (n - 1)  # clear lowest set bit
        count += 1
    return count
```

**Simple version:**
```python
def hammingWeight(n: int) -> int:
    count = 0
    while n:
        count += n & 1
        n >>= 1
    return count
```

**Time:** O(1) — at most 32 iterations | **Space:** O(1)

### Interview Traps

**Trap 1 — Using Python's bin() shortcut**
`bin(n).count('1')` works in Python but is not the expected algorithmic answer.  
✅ Implement the bit manipulation directly.

**Trap 2 — Not knowing Brian Kernighan's trick**
`n & (n-1)` is a classic bit trick. Know it and explain why it works: subtracting 1 flips all bits from the lowest set bit rightward, and ANDing clears that bit.

**Trap 3 — Right-shifting signed integers in other languages**
In Java/C++, right-shift of a signed integer is arithmetic (fills with sign bit). Use unsigned right shift `>>>` in Java.

**Trap 4 — "What's the Hamming distance between two numbers?"**
`hammingWeight(x XOR y)` — XOR highlights differing bits, then count them.

---

## 146. Counting Bits

**LeetCode #338 | Difficulty: 🟢 Easy**

### Problem Statement

Given an integer `n`, return an array of length `n+1` where `ans[i]` is the number of 1s in the binary representation of `i`.

```
Input:  n = 2
Output: [0,1,1]

Input:  n = 5
Output: [0,1,1,2,1,2]
```

### Intuition

**DP insight:** For any number `i`, the number of 1 bits = the number of 1 bits in `i >> 1` (right shift = remove last bit) plus the last bit (`i & 1`).

`dp[i] = dp[i >> 1] + (i & 1)`

This processes each number in O(1) using previously computed results.

### Solution

```python
def countBits(n: int) -> list[int]:
    dp = [0] * (n + 1)
    for i in range(1, n + 1):
        dp[i] = dp[i >> 1] + (i & 1)
    return dp
```

**Time:** O(n) | **Space:** O(n)

### Interview Traps

**Trap 1 — Using hammingWeight for each number**
O(n · 32) — technically O(n) since 32 is constant, but O(n) DP is more elegant and shows pattern recognition.

**Trap 2 — Alternative DP: using lowest set bit**
`dp[i] = dp[i & (i-1)] + 1` — remove lowest set bit, count is one more than that result.

**Trap 3 — "Explain the recurrence"**
`i >> 1` drops the last bit (same as `i // 2`). The 1-bit count of `i` equals the count of `i // 2` plus whether `i` is odd.

---

## 147. Reverse Bits

**LeetCode #190 | Difficulty: 🟢 Easy**

### Problem Statement

Reverse the bits of a given 32-bit unsigned integer.

```
Input:  n = 43261596 (00000010100101000001111010011100)
Output: 964176192  (00111001011110000010100101000000)
```

### Intuition

For each of the 32 bit positions, extract the least significant bit of `n` (using `n & 1`), place it at the current result position (by OR-ing into `result`), shift `result` left, and shift `n` right. Repeat 32 times.

### Solution

```python
def reverseBits(n: int) -> int:
    result = 0
    for _ in range(32):
        result = (result << 1) | (n & 1)
        n >>= 1
    return result
```

**Time:** O(1) — fixed 32 iterations | **Space:** O(1)

### Interview Traps

**Trap 1 — Forgetting to process all 32 bits**
Even if `n` becomes 0 early, you must continue to shift `result` to the correct position for all 32 bits.

**Trap 2 — "What if called many times?" (caching)**
Cache results in chunks of 8 bits (byte-level lookup table). Process 4 bytes separately and combine.
```python
cache = {}
# Split n into 4 bytes, reverse each byte, combine in reversed order
```

**Trap 3 — Python's arbitrary precision integers**
Python integers don't have a fixed 32-bit size. Always process exactly 32 iterations and mask the result: `result & 0xFFFFFFFF`.

**Trap 4 — String reversal shortcut**
`int(bin(n)[2:].zfill(32)[::-1], 2)` works in Python but is not the algorithmic answer the interviewer expects.

---

## 148. Missing Number

**LeetCode #268 | Difficulty: 🟢 Easy**

### Problem Statement

Given an array `nums` containing `n` distinct numbers in the range `[0, n]`, return the one number that is missing.

```
Input:  nums = [3,0,1]
Output: 2

Input:  nums = [9,6,4,2,3,5,7,0,1]
Output: 8
```

### Intuition

**XOR approach:** XOR all indices `0..n` with all array values. Every number present cancels out, leaving the missing one.

**Math approach:** `expected_sum = n*(n+1)//2`. `missing = expected_sum - sum(nums)`.

### Solution

**XOR approach:**
```python
def missingNumber(nums: list[int]) -> int:
    result = len(nums)
    for i, num in enumerate(nums):
        result ^= i ^ num
    return result
```

**Math approach:**
```python
def missingNumber(nums: list[int]) -> int:
    n = len(nums)
    return n * (n + 1) // 2 - sum(nums)
```

**Time:** O(n) | **Space:** O(1)

### Interview Traps

**Trap 1 — Starting XOR from 0 and forgetting n**
XOR all values `0` through `n` (inclusive). Initialize `result = n` and XOR with both `i` and `nums[i]` in the loop.

**Trap 2 — Integer overflow in sum approach**
In languages with fixed-size integers, `n*(n+1)//2` can overflow for large n. XOR avoids this entirely.

**Trap 3 — "What if there are two missing numbers?"**
Math/XOR tricks break. Use a HashSet or sort to find both.

**Trap 4 — "What if numbers can repeat?" (Find the Duplicate)**
Different problem — see LeetCode #287 (Floyd's cycle detection).

---

## 149. Sum of Two Integers

**LeetCode #371 | Difficulty: 🟡 Medium**

### Problem Statement

Given two integers `a` and `b`, return their sum **without using the `+` or `-` operators**.

```
Input:  a = 1, b = 2
Output: 3

Input:  a = 2, b = 3
Output: 5
```

### Intuition

Use **bitwise operations** to simulate addition:
- **XOR** (`a ^ b`) gives the sum without carry.
- **AND + left shift** (`(a & b) << 1`) gives the carry.

Repeat until there's no carry.

Handle Python's arbitrary-precision integers with a 32-bit mask.

### Solution

```python
def getSum(a: int, b: int) -> int:
    MASK = 0xFFFFFFFF
    MAX = 0x7FFFFFFF

    while b & MASK:
        carry = (a & b) << 1
        a = a ^ b
        b = carry

    # If result fits in 32-bit signed, return as-is; else convert from two's complement
    return a if a <= MAX else ~(a ^ MASK)
```

**Time:** O(1) — at most 32 iterations | **Space:** O(1)

### Interview Traps

**Trap 1 — Infinite loop in Python**
Python integers are arbitrary precision — carries can propagate indefinitely. Mask with `0xFFFFFFFF` to constrain to 32 bits.

**Trap 2 — Not handling negative results**
After masking, check if the result exceeds `MAX_INT`. If so, convert from two's complement: `~(a ^ MASK)`.

**Trap 3 — Forgetting the carry loop**
One round of XOR + AND handles one bit position. You need to loop until the carry is zero.

**Trap 4 — "Why does XOR give sum without carry?"**
XOR is addition in GF(2) — it adds bits without propagating carry. AND captures where both bits are 1 (i.e., where carry occurs). Shifting left by 1 propagates the carry to the next position.

---

## 150. Reverse Integer

**LeetCode #7 | Difficulty: 🟡 Medium**

### Problem Statement

Given a signed 32-bit integer `x`, return `x` with its digits reversed. If reversing causes overflow outside `[-2³¹, 2³¹-1]`, return 0.

```
Input:  x = 123
Output: 321

Input:  x = -123
Output: -321

Input:  x = 120
Output: 21
```

### Intuition

Extract digits one by one from the right using `% 10` and build the reversed number by multiplying the result by 10 and adding each digit. Check for overflow before each multiplication.

### Solution

```python
def reverse(x: int) -> int:
    INT_MIN, INT_MAX = -2**31, 2**31 - 1
    result = 0
    sign = 1 if x >= 0 else -1
    x = abs(x)

    while x:
        digit = x % 10
        x //= 10

        # Check overflow before updating result
        if result > (INT_MAX - digit) // 10:
            return 0
        result = result * 10 + digit

    return sign * result
```

**Time:** O(log x) — number of digits | **Space:** O(1)

### Interview Traps

**Trap 1 — Not checking overflow before multiplying**
`result * 10 + digit` can overflow. Check `result > (INT_MAX - digit) // 10` before the operation.

**Trap 2 — Python doesn't overflow — but the problem requires 32-bit behavior**
In Python, integers never overflow. Manually check bounds against `2**31 - 1` and `-2**31`.

**Trap 3 — Handling negative numbers**
Extract the sign, work with `abs(x)`, reapply sign at the end.

**Trap 4 — Trailing zeros become leading zeros**
`120` reversed is `021` = `21`. The `% 10` / `// 10` loop handles this naturally — trailing zeros produce leading zeros which don't affect the integer value.

**Trap 5 — String reversal shortcut**
`int(str(abs(x))[::-1]) * sign` works but doesn't handle overflow correctly — you'd need to check after reversal. The digit-by-digit approach integrates the overflow check more cleanly.

---

*[← Back to Index](./index.md) | [Next: Arrays Extra (Grind 169) →](./19_arrays_extra.md)*

---

## 🎉 Congratulations!

You've completed the full NeetCode 150 Interview Handbook. Here's a quick recap of what you've covered:

| Topic | Problems | Key Patterns |
|-------|----------|-------------|
| Arrays & Hashing | 9 | HashSet, HashMap, Frequency Count |
| Two Pointers | 5 | Sorted arrays, palindromes, sliding extremes |
| Sliding Window | 6 | Fixed/variable window, deque |
| Stack | 7 | Monotonic stack, expression eval |
| Binary Search | 7 | Search space reduction, answer binary search |
| Linked List | 11 | Fast/slow pointers, dummy head |
| Trees | 15 | DFS/BFS, BST properties |
| Tries | 3 | Prefix trees, wildcard matching |
| Heap | 7 | Min/max heap, two-heap trick |
| Backtracking | 9 | Choose/explore/unchoose |
| Graphs | 13 | BFS/DFS, Union-Find, topological sort |
| Advanced Graphs | 6 | Dijkstra, Bellman-Ford, MST |
| 1-D DP | 12 | Memoization, bottom-up DP |
| 2-D DP | 11 | Grid DP, string DP |
| Greedy | 8 | Local optimum = global optimum |
| Intervals | 6 | Sort by start/end, sweep line |
| Math & Geometry | 8 | Matrix ops, fast exponentiation |
| Bit Manipulation | 7 | XOR, bit masking |

**Good luck with your interviews!** 🚀

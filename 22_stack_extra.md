# Stack — Extra Problems

**Grind 169 Supplement | Not in NeetCode 150**  
[← Back to Index](./index.md)

---

## Problems in this Section

| # | Problem | Difficulty |
|---|---------|------------|
| 1 | [Implement Queue using Stacks](#1-implement-queue-using-stacks) | 🟢 Easy |
| 2 | [Backspace String Compare](#2-backspace-string-compare) | 🟢 Easy |
| 3 | [Decode String](#3-decode-string) | 🟡 Medium |
| 4 | [Asteroid Collision](#4-asteroid-collision) | 🟡 Medium |
| 5 | [Basic Calculator II](#5-basic-calculator-ii) | 🟡 Medium |
| 6 | [Basic Calculator](#6-basic-calculator) | 🔴 Hard |
| 7 | [Maximum Frequency Stack](#7-maximum-frequency-stack) | 🔴 Hard |
| 8 | [Longest Valid Parentheses](#8-longest-valid-parentheses) | 🔴 Hard |

---

## 1. Implement Queue using Stacks

**LeetCode #232 | Difficulty: 🟢 Easy**

### Problem Statement

Implement a FIFO queue using only two stacks. Support `push`, `pop`, `peek`, and `empty`.

```
MyQueue q = new MyQueue()
q.push(1); q.push(2)
q.peek()  → 1
q.pop()   → 1
q.empty() → false
```

### Intuition

Use two stacks: `inbox` (for push) and `outbox` (for pop/peek). When `outbox` is empty, pour all of `inbox` into it — this reverses order to give FIFO behavior. The key insight: only transfer when `outbox` is empty, giving **amortized O(1)** per operation.

### Solution

```python
class MyQueue:
    def __init__(self):
        self.inbox = []   # for push
        self.outbox = []  # for pop/peek

    def push(self, x: int) -> None:
        self.inbox.append(x)

    def _transfer(self):
        if not self.outbox:
            while self.inbox:
                self.outbox.append(self.inbox.pop())

    def pop(self) -> int:
        self._transfer()
        return self.outbox.pop()

    def peek(self) -> int:
        self._transfer()
        return self.outbox[-1]

    def empty(self) -> bool:
        return not self.inbox and not self.outbox
```

**Time:** O(1) amortized | **Space:** O(n)

### Interview Traps

**Trap 1 — Transferring on every push**
Transferring inbox → outbox on every push makes pop O(n). Transfer only when outbox is empty.

**Trap 2 — Not handling peek correctly**
`peek` should return without removing. Only `pop` removes.

**Trap 3 — "What is the worst-case time for pop?"**
O(n) in the worst case (first pop after n pushes). But amortized across n operations it is O(1) — each element is moved at most once.

**Trap 4 — Symmetric problem: Implement Stack using Queues (LeetCode #225)**
Push is O(n): rotate the queue so new element is at front. Know both directions.

---

## 2. Backspace String Compare

**LeetCode #844 | Difficulty: 🟢 Easy**

### Problem Statement

Given two strings `s` and `t` where `#` represents a backspace, return `true` if they are equal after processing.

```
Input:  s = "ab#c", t = "ad#c"
Output: true  (both become "ac")

Input:  s = "ab##", t = "c#d#"
Output: true  (both become "")
```

### Intuition

**Stack approach:** Simulate typing — push characters, pop on `#`. Compare final stacks.

**Two-pointer O(1) space:** Process both strings from right to left, skipping characters consumed by backspaces, comparing character by character.

### Solution

**Stack approach:**
```python
def backspaceCompare(s: str, t: str) -> bool:
    def process(string):
        stack = []
        for c in string:
            if c == '#':
                if stack:
                    stack.pop()
            else:
                stack.append(c)
        return stack

    return process(s) == process(t)
```

**Two-pointer O(1) space:**
```python
def backspaceCompare(s: str, t: str) -> bool:
    i, j = len(s) - 1, len(t) - 1
    skip_s = skip_t = 0

    while i >= 0 or j >= 0:
        while i >= 0:
            if s[i] == '#':
                skip_s += 1; i -= 1
            elif skip_s > 0:
                skip_s -= 1; i -= 1
            else:
                break
        while j >= 0:
            if t[j] == '#':
                skip_t += 1; j -= 1
            elif skip_t > 0:
                skip_t -= 1; j -= 1
            else:
                break
        if i >= 0 and j >= 0 and s[i] != t[j]:
            return False
        if (i >= 0) != (j >= 0):
            return False
        i -= 1; j -= 1

    return True
```

**Time:** O(n) | **Space:** O(1) two-pointer

### Interview Traps

**Trap 1 — Stopping when one string is exhausted**
Both strings must be fully processed — one might have trailing backspaces.

**Trap 2 — Backspace on empty string**
`#` with an empty stack/skip count = 0 → just ignore. Both solutions handle this.

**Trap 3 — Not knowing the O(1) space approach**
Start with the stack version, then offer the two-pointer optimization if asked for O(1) space.

---

## 3. Decode String

**LeetCode #394 | Difficulty: 🟡 Medium**

### Problem Statement

Given an encoded string, return the decoded string. The encoding rule is `k[encoded_string]` where the encoded string inside brackets is repeated exactly `k` times.

```
Input:  s = "3[a]2[bc]"
Output: "aaabcbc"

Input:  s = "3[a2[c]]"
Output: "accaccacc"
```

### Intuition

Use a **stack** to handle nesting. When encountering:
- A digit: build the full number.
- `[`: push current string and number onto the stack, reset both.
- `]`: pop number and previous string, repeat current string `k` times and append to previous.
- A letter: append to current string.

### Solution

```python
def decodeString(s: str) -> str:
    stack = []  # stores (current_string, repeat_count)
    curr_str = ""
    curr_num = 0

    for c in s:
        if c.isdigit():
            curr_num = curr_num * 10 + int(c)
        elif c == '[':
            stack.append((curr_str, curr_num))
            curr_str = ""
            curr_num = 0
        elif c == ']':
            prev_str, num = stack.pop()
            curr_str = prev_str + num * curr_str
        else:
            curr_str += c

    return curr_str
```

**Time:** O(n · k) where k = max repeat count | **Space:** O(n)

### Interview Traps

**Trap 1 — Multi-digit numbers**
`10[a]` has repeat count 10, not 1 followed by 0. Build numbers with `curr_num = curr_num * 10 + int(c)`.

**Trap 2 — Nested brackets**
`2[3[a]]` → `2[aaa]` → `aaaaaa`. The stack naturally handles nesting by saving and restoring context.

**Trap 3 — Recursive approach**
Use an index pointer and recurse on `[...]` blocks. Cleaner conceptually but uses O(depth) call stack.

**Trap 4 — Not resetting curr_num and curr_str after `[`**
After pushing to stack, always reset both to their empty defaults.

---

## 4. Asteroid Collision

**LeetCode #735 | Difficulty: 🟡 Medium**

### Problem Statement

Given an array of integers `asteroids` (positive = right, negative = left, absolute value = size), simulate collisions. When two asteroids meet, the smaller one explodes. Equal size: both explode. Return the final state.

```
Input:  asteroids = [5,10,-5]
Output: [5,10]  (10 and -5 collide, 10 survives)

Input:  asteroids = [8,-8]
Output: []  (both explode)

Input:  asteroids = [10,2,-5]
Output: [10]
```

### Intuition

Use a stack. For each asteroid:
- If moving right (positive) or stack is empty or stack top is moving left (negative) → push (no collision possible).
- If moving left (negative) and stack top is moving right → collision:
  - Stack top smaller → pop it, continue checking.
  - Stack top larger → current asteroid explodes (skip it).
  - Equal size → both explode (pop stack top, skip current).

### Solution

```python
def asteroidCollision(asteroids: list[int]) -> list[int]:
    stack = []

    for a in asteroids:
        alive = True
        while alive and a < 0 and stack and stack[-1] > 0:
            if stack[-1] < -a:
                stack.pop()  # stack top explodes, current continues
            elif stack[-1] == -a:
                stack.pop()  # both explode
                alive = False
            else:
                alive = False  # current explodes
        if alive:
            stack.append(a)

    return stack
```

**Time:** O(n) | **Space:** O(n)

### Interview Traps

**Trap 1 — Two positive or two negative asteroids never collide**
Only a right-moving (positive) followed by a left-moving (negative) causes collision.

**Trap 2 — Not looping after one collision**
After destroying the stack top, the current asteroid might collide with the next one. Keep checking in the `while` loop.

**Trap 3 — Equal size handling**
Both explode — pop the stack top AND mark the current as dead (`alive = False`).

**Trap 4 — Negative asteroid with empty stack**
A left-moving asteroid with nothing to its left just passes through — push it.

---

## 5. Basic Calculator II

**LeetCode #227 | Difficulty: 🟡 Medium**

### Problem Statement

Implement a calculator to evaluate a string expression with `+`, `-`, `*`, `/` (integer division truncating toward zero). No parentheses.

```
Input:  s = "3+2*2"
Output: 7

Input:  s = " 3/2 "
Output: 1

Input:  s = " 3+5 / 2 "
Output: 5
```

### Intuition

Use a stack to handle operator precedence. For `+` and `-`, push the number (negated for `-`) onto the stack. For `*` and `/`, pop the top, compute immediately (higher precedence), and push the result. Sum the stack at the end.

### Solution

```python
def calculate(s: str) -> int:
    stack = []
    num = 0
    op = '+'  # default operator before first number

    for i, c in enumerate(s):
        if c.isdigit():
            num = num * 10 + int(c)

        if (not c.isdigit() and c != ' ') or i == len(s) - 1:
            if op == '+':
                stack.append(num)
            elif op == '-':
                stack.append(-num)
            elif op == '*':
                stack.append(stack.pop() * num)
            elif op == '/':
                stack.append(int(stack.pop() / num))  # truncate toward zero
            op = c
            num = 0

    return sum(stack)
```

**Time:** O(n) | **Space:** O(n)

### Interview Traps

**Trap 1 — Not handling multi-digit numbers**
Build numbers with `num = num * 10 + int(c)`.

**Trap 2 — Processing last number**
The last number is processed when `i == len(s) - 1`, since there's no operator after it.

**Trap 3 — Python floor division vs truncation toward zero**
`-7 // 2 = -4` (floor) but `-7 / 2` truncated = `-3`.  
✅ Use `int(stack.pop() / num)` not `stack.pop() // num`.

**Trap 4 — "What if parentheses are added?" (Basic Calculator I)**
That's the next problem — requires a fundamentally different recursive/stack approach.

---

## 6. Basic Calculator

**LeetCode #224 | Difficulty: 🔴 Hard**

### Problem Statement

Implement a calculator to evaluate a string expression with `+`, `-`, and parentheses `()`. No `*` or `/`.

```
Input:  s = "1 + 1"
Output: 2

Input:  s = " 2-1 + 2 "
Output: 3

Input:  s = "(1+(4+5+2)-3)+(6+8)"
Output: 23
```

### Intuition

Use a stack to save context when entering parentheses. Track `result` (running total) and `sign` (current sign). When hitting `(`, push current result and sign onto stack and reset. When hitting `)`, pop and combine.

### Solution

```python
def calculate(s: str) -> int:
    stack = []
    result = 0
    num = 0
    sign = 1  # +1 or -1

    for c in s:
        if c.isdigit():
            num = num * 10 + int(c)
        elif c == '+':
            result += sign * num
            num = 0
            sign = 1
        elif c == '-':
            result += sign * num
            num = 0
            sign = -1
        elif c == '(':
            # Save current state
            stack.append(result)
            stack.append(sign)
            result = 0
            sign = 1
        elif c == ')':
            result += sign * num
            num = 0
            result *= stack.pop()   # apply sign before '('
            result += stack.pop()   # add result before '('

    result += sign * num
    return result
```

**Time:** O(n) | **Space:** O(n)

### Interview Traps

**Trap 1 — Not handling multi-digit numbers**
Build numbers digit by digit before processing an operator.

**Trap 2 — Forgetting to process the last number**
After the loop ends, `result += sign * num` handles the last pending number.

**Trap 3 — Wrong pop order after `)`**
Push `result` then `sign` (result first, sign second). Pop in reverse: sign first (`stack.pop()`), then result (`stack.pop()`).

**Trap 4 — Extending to `*` and `/` with parentheses**
Combine both approaches: use a recursive descent parser or a full Shunting-Yard algorithm.

---

## 7. Maximum Frequency Stack

**LeetCode #895 | Difficulty: 🔴 Hard**

### Problem Statement

Design a stack that pushes integers and pops the most frequent element. If tie in frequency, pop the most recently pushed among most frequent.

```
FreqStack fs = new FreqStack()
fs.push(5); fs.push(7); fs.push(5); fs.push(7); fs.push(4); fs.push(5)
fs.pop() → 5  (freq 3)
fs.pop() → 7  (freq 2, most recent among freq-2)
fs.pop() → 5  (freq 2)
fs.pop() → 4  (freq 1)
```

### Intuition

Maintain:
- `freq`: HashMap of element → current frequency.
- `group`: HashMap of frequency → stack of elements at that frequency.
- `max_freq`: current maximum frequency.

On `push`: increment `freq[x]`, append `x` to `group[freq[x]]`, update `max_freq`.
On `pop`: pop from `group[max_freq]`, decrement `freq[x]`, decrease `max_freq` if that group is now empty.

### Solution

```python
from collections import defaultdict

class FreqStack:
    def __init__(self):
        self.freq = defaultdict(int)
        self.group = defaultdict(list)
        self.max_freq = 0

    def push(self, val: int) -> None:
        self.freq[val] += 1
        f = self.freq[val]
        self.max_freq = max(self.max_freq, f)
        self.group[f].append(val)

    def pop(self) -> int:
        val = self.group[self.max_freq].pop()
        self.freq[val] -= 1
        if not self.group[self.max_freq]:
            self.max_freq -= 1
        return val
```

**Time:** O(1) push and pop | **Space:** O(n)

### Interview Traps

**Trap 1 — Using a heap**
A heap gives O(log n) per operation. The group-based approach achieves O(1).

**Trap 2 — Not maintaining all frequency levels in group**
When an element is pushed with freq=3, it must also exist in group[1] and group[2]. The push appends to exactly `group[freq[x]]` — previous levels are already populated from earlier pushes.

**Trap 3 — max_freq can only decrease by 1 on pop**
After popping from `max_freq`, if that group is empty, `max_freq -= 1`. It can never skip a level because frequencies are always consecutive.

---

## 8. Longest Valid Parentheses

**LeetCode #32 | Difficulty: 🔴 Hard**

### Problem Statement

Given a string containing only `(` and `)`, return the length of the longest valid (well-formed) parentheses substring.

```
Input:  s = "(()"
Output: 2

Input:  s = ")()())"
Output: 4

Input:  s = ""
Output: 0
```

### Intuition

Use a **stack** storing indices. Initialize with `-1` as a base. On `(`, push its index. On `)`, pop the top:
- If stack is empty after popping → push current index as new base.
- Otherwise → current length = `current_index - stack[-1]`.

### Solution

```python
def longestValidParentheses(s: str) -> int:
    stack = [-1]  # base index
    max_len = 0

    for i, c in enumerate(s):
        if c == '(':
            stack.append(i)
        else:
            stack.pop()
            if not stack:
                stack.append(i)  # new base
            else:
                max_len = max(max_len, i - stack[-1])

    return max_len
```

**DP approach:**
```python
def longestValidParentheses(s: str) -> int:
    n = len(s)
    dp = [0] * n  # dp[i] = length of longest valid ending at i

    for i in range(1, n):
        if s[i] == ')':
            if s[i-1] == '(':
                dp[i] = (dp[i-2] if i >= 2 else 0) + 2
            elif dp[i-1] > 0:
                j = i - dp[i-1] - 1
                if j >= 0 and s[j] == '(':
                    dp[i] = dp[i-1] + 2 + (dp[j-1] if j >= 1 else 0)

    return max(dp) if dp else 0
```

**Time:** O(n) | **Space:** O(n)

### Interview Traps

**Trap 1 — Counting matched pairs instead of tracking indices**
Counting `(` and `)` naively can't handle gaps like `)(()`.  
✅ The index stack approach handles all cases correctly.

**Trap 2 — Not initializing stack with -1**
The `-1` sentinel enables computing the length of valid substrings starting at index 0.

**Trap 3 — Two-pass O(1) space approach**
Left-to-right: count `left` and `right`. When equal, update max. When `right > left`, reset both. Right-to-left: do the same. Handles all cases with O(1) space.

**Trap 4 — Confusing with Valid Parentheses (LC #20)**
LC #20 checks if a whole string is valid. This problem finds the longest valid substring within a string that may be partially invalid.

---

*[← Back to Index](./index.md) | [Next: Strings Extra →](./23_strings_extra.md)*

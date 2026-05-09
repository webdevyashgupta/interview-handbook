# Stack

**Topic 4 of 18 | NeetCode 150 Interview Handbook**  
[← Back to Index](./index.md)

---

## Problems in this Section

| # | Problem | Difficulty |
|---|---------|------------|
| 21 | [Valid Parentheses](#21-valid-parentheses) | 🟢 Easy |
| 22 | [Min Stack](#22-min-stack) | 🟡 Medium |
| 23 | [Evaluate Reverse Polish Notation](#23-evaluate-reverse-polish-notation) | 🟡 Medium |
| 24 | [Generate Parentheses](#24-generate-parentheses) | 🟡 Medium |
| 25 | [Daily Temperatures](#25-daily-temperatures) | 🟡 Medium |
| 26 | [Car Fleet](#26-car-fleet) | 🟡 Medium |
| 27 | [Largest Rectangle in Histogram](#27-largest-rectangle-in-histogram) | 🔴 Hard |

---

## 21. Valid Parentheses

**LeetCode #20 | Difficulty: 🟢 Easy**

### Problem Statement

Given a string `s` containing only `(`, `)`, `{`, `}`, `[`, `]`, determine if the input string is valid. A string is valid if open brackets are closed by the same type of bracket and in the correct order.

```
Input:  s = "()[]{}"
Output: true

Input:  s = "(]"
Output: false

Input:  s = "([)]"
Output: false
```

### Intuition

Use a **stack**. For every opening bracket, push it. For every closing bracket, check if the top of the stack is the matching opener. If it isn't (or the stack is empty), return `False`. At the end, the stack must be empty.

### Solution

```python
def isValid(s: str) -> bool:
    stack = []
    matching = {')': '(', '}': '{', ']': '['}

    for char in s:
        if char in matching:
            if not stack or stack[-1] != matching[char]:
                return False
            stack.pop()
        else:
            stack.append(char)

    return len(stack) == 0
```

**Time:** O(n) | **Space:** O(n)

### Interview Traps

**Trap 1 — Forgetting to check if stack is empty before popping**
If the string starts with a closing bracket, `stack[-1]` raises an IndexError.  
✅ Always check `not stack` before accessing `stack[-1]`.

**Trap 2 — Not checking stack is empty at the end**
`"((("` would be considered valid if you only check mismatches during traversal.  
✅ Return `len(stack) == 0`.

**Trap 3 — Hardcoding three separate conditions**
Using a mapping dict `{')': '(', ...}` is cleaner and more extensible than `if char == ')' and stack[-1] == '('`.

**Trap 4 — "What if other characters appear in the string?"**
The current implementation pushes non-bracket chars onto the stack. Clarify: *"The problem guarantees only bracket characters."* If not guaranteed, add a filter.

**Trap 5 — Even-length shortcut**
Some candidates add `if len(s) % 2 != 0: return False`. Correct optimization but not necessary — mention it as a micro-optimization.

---

## 22. Min Stack

**LeetCode #155 | Difficulty: 🟡 Medium**

### Problem Statement

Design a stack that supports `push`, `pop`, `top`, and `getMin` — all in **O(1)** time.

```
MinStack minStack = new MinStack();
minStack.push(-2);
minStack.push(0);
minStack.push(-3);
minStack.getMin(); // -3
minStack.pop();
minStack.top();    // 0
minStack.getMin(); // -2
```

### Intuition

Maintain two stacks: one regular stack and one **min-tracking stack**. The min stack stores the current minimum at each level — when you push a value, push `min(value, current_min)` onto the min stack. When you pop, pop from both stacks. `getMin()` peeks the top of the min stack.

### Solution

```python
class MinStack:
    def __init__(self):
        self.stack = []
        self.min_stack = []

    def push(self, val: int) -> None:
        self.stack.append(val)
        current_min = min(val, self.min_stack[-1] if self.min_stack else val)
        self.min_stack.append(current_min)

    def pop(self) -> None:
        self.stack.pop()
        self.min_stack.pop()

    def top(self) -> int:
        return self.stack[-1]

    def getMin(self) -> int:
        return self.min_stack[-1]
```

**Time:** O(1) all operations | **Space:** O(n)

### Interview Traps

**Trap 1 — Maintaining a single min variable**
A single `self.min` breaks when the minimum element is popped — you don't know what the new minimum is.  
✅ The min stack restores the previous minimum after every pop.

**Trap 2 — Only pushing to min_stack when a new minimum is found**
If you only push on new minimums, pop doesn't align with the main stack — the stacks get out of sync.  
✅ Always push to `min_stack` on every `push`, always pop on every `pop`.

**Trap 3 — Empty stack operations**
Calling `top()`, `pop()`, or `getMin()` on an empty stack is undefined per the problem — but mention you'd add guard checks in production.

**Trap 4 — "Can you do it with O(1) extra space?"**
Yes — store `(value, current_min)` tuples in a single stack instead of two parallel stacks.
```python
self.stack.append((val, current_min))
# getMin: return self.stack[-1][1]
```

---

## 23. Evaluate Reverse Polish Notation

**LeetCode #150 | Difficulty: 🟡 Medium**

### Problem Statement

Evaluate an expression in Reverse Polish Notation (postfix). Valid operators are `+`, `-`, `*`, `/`. Division truncates toward zero.

```
Input:  tokens = ["2","1","+","3","*"]
Output: 9  ((2+1)*3)

Input:  tokens = ["4","13","5","/","+"]
Output: 6  (4+(13/5))
```

### Intuition

RPN is designed for stack evaluation. For each token:
- If it's a number → push onto stack.
- If it's an operator → pop two operands, apply the operator, push the result.

The final value on the stack is the answer.

### Solution

```python
def evalRPN(tokens: list[str]) -> int:
    stack = []
    operators = {
        '+': lambda a, b: a + b,
        '-': lambda a, b: a - b,
        '*': lambda a, b: a * b,
        '/': lambda a, b: int(a / b),  # truncate toward zero
    }

    for token in tokens:
        if token in operators:
            b = stack.pop()
            a = stack.pop()
            stack.append(operators[token](a, b))
        else:
            stack.append(int(token))

    return stack[0]
```

**Time:** O(n) | **Space:** O(n)

### Interview Traps

**Trap 1 — Operand order for subtraction and division**
`a - b` and `a / b` where `a` is the first popped and `b` is the second is wrong. The first operand is deeper in the stack.
```python
b = stack.pop()  # top of stack = second operand
a = stack.pop()  # below = first operand
result = a - b   # correct order
```

**Trap 2 — Integer division truncating toward zero vs floor**
Python's `//` is floor division: `-7 // 2 = -4`. The problem requires truncation toward zero: `-7 / 2 = -3`.  
✅ Use `int(a / b)` not `a // b`.

**Trap 3 — Parsing negative number tokens**
Tokens like `"-3"` must be parsed as negative integers, not operators.  
✅ `int(token)` handles this correctly since `"-3"` is not in the operators dict.

**Trap 4 — Empty stack after evaluation**
A valid RPN expression always leaves exactly one value on the stack. If there are more or fewer, the input is malformed (though the problem guarantees valid input).

---

## 24. Generate Parentheses

**LeetCode #22 | Difficulty: 🟡 Medium**

### Problem Statement

Given `n` pairs of parentheses, generate all combinations of well-formed parentheses.

```
Input:  n = 3
Output: ["((()))","(()())","(())()","()(())","()()()"]
```

### Intuition

Use **backtracking**. At each step, decide to add `(` or `)` with two constraints:
- Add `(` only if `open_count < n`
- Add `)` only if `close_count < open_count`

When `open_count == close_count == n`, we have a valid combination.

### Solution

```python
def generateParenthesis(n: int) -> list[str]:
    result = []

    def backtrack(current, open_count, close_count):
        if len(current) == 2 * n:
            result.append(current)
            return
        if open_count < n:
            backtrack(current + '(', open_count + 1, close_count)
        if close_count < open_count:
            backtrack(current + ')', open_count, close_count + 1)

    backtrack('', 0, 0)
    return result
```

**Time:** O(4ⁿ / √n) — Catalan number | **Space:** O(n) recursion depth

### Interview Traps

**Trap 1 — Generating all strings and filtering**
Generating all 2^(2n) binary strings and checking validity is exponentially wasteful.  
✅ Pruning during generation (the two constraints) is far more efficient.

**Trap 2 — Wrong constraint for closing bracket**
`close_count < n` is wrong — you'd generate `))((`-style strings.  
✅ The correct constraint is `close_count < open_count`.

**Trap 3 — Mutating a shared list**
```python
# ❌ Mutates shared list — causes bugs
current.append('(')
backtrack(current, ...)
current.pop()

# ✅ Safe — creates new string each call
backtrack(current + '(', ...)
```
Using string concatenation is slightly less efficient but safer and cleaner.

**Trap 4 — "How many valid combinations exist for n pairs?"**
The answer is the nth Catalan number: `C(n) = C(2n, n) / (n+1)`. Knowing this shows mathematical depth.

---

## 25. Daily Temperatures

**LeetCode #739 | Difficulty: 🟡 Medium**

### Problem Statement

Given an array `temperatures`, return an array `answer` where `answer[i]` is the number of days until a warmer temperature. If there is no future warmer day, `answer[i] = 0`.

```
Input:  temperatures = [73,74,75,71,69,72,76,73]
Output: [1,1,4,2,1,1,0,0]
```

### Intuition

Use a **monotonic decreasing stack** (stores indices of temperatures in decreasing order). For each new temperature:
- While the stack is non-empty and the current temperature is warmer than the temperature at the stack's top index → pop and compute the wait days.
- Push the current index.

### Solution

```python
def dailyTemperatures(temperatures: list[int]) -> list[int]:
    answer = [0] * len(temperatures)
    stack = []  # stores indices, monotonically decreasing by temperature

    for i, temp in enumerate(temperatures):
        while stack and temperatures[stack[-1]] < temp:
            prev_idx = stack.pop()
            answer[prev_idx] = i - prev_idx
        stack.append(i)

    return answer
```

**Time:** O(n) | **Space:** O(n)

### Interview Traps

**Trap 1 — Brute force O(n²)**
For each day, scanning forward to find a warmer day is O(n²).  
✅ The monotonic stack achieves O(n) — each index is pushed and popped at most once.

**Trap 2 — Storing temperatures instead of indices**
You need the index to compute `i - prev_idx`.  
✅ Store indices in the stack; retrieve temperatures via `temperatures[stack[-1]]`.

**Trap 3 — Strict vs non-strict comparison**
`temperatures[stack[-1]] < temp` (strict) is correct — "warmer" means strictly greater.

**Trap 4 — Leftover stack elements**
Indices remaining in the stack at the end have no warmer future day. `answer` is initialized to `0`, so no extra cleanup is needed.

**Trap 5 — "What is a monotonic stack?"**
Be ready to explain: a stack that maintains elements in a monotonically increasing or decreasing order by discarding elements that violate the ordering when a new element arrives.

---

## 26. Car Fleet

**LeetCode #853 | Difficulty: 🟡 Medium**

### Problem Statement

`n` cars are heading to a target destination. Given arrays `position` and `speed`, a car that catches up to a car ahead forms a **fleet** and travels at the slower car's speed. Return the number of fleets that arrive at the destination.

```
Input:  target = 12, position = [10,8,0,5,3], speed = [2,4,1,1,3]
Output: 3
```

### Intuition

Sort cars by position in **descending order** (closest to target first). Compute each car's **time to reach target**: `(target - position[i]) / speed[i]`.

Use a stack of arrival times. If the current car's time is greater than the top of the stack, it cannot catch the car ahead → it forms a new fleet (push). Otherwise, it merges into the fleet ahead (skip — it arrives no later than the car ahead).

### Solution

```python
def carFleet(target: int, position: list[int], speed: list[int]) -> int:
    pairs = sorted(zip(position, speed), reverse=True)
    stack = []

    for pos, spd in pairs:
        time = (target - pos) / spd
        if not stack or time > stack[-1]:
            stack.append(time)
        # else: merges into the fleet ahead, do nothing

    return len(stack)
```

**Time:** O(n log n) | **Space:** O(n)

### Interview Traps

**Trap 1 — Not sorting by position**
Processing cars in arbitrary order makes fleet detection impossible.  
✅ Sort by position descending (closest to target first).

**Trap 2 — Using integer division**
`(target - pos) // spd` loses fractional time, causing incorrect fleet merging.  
✅ Use float division `/`.

**Trap 3 — "Why process closest cars first?"**
A car behind can only catch a car ahead, not the other way around. Processing front-to-back lets us determine which cars will be caught before they merge.

**Trap 4 — Equal arrival times**
If two cars have equal arrival times, they arrive simultaneously → they form one fleet.  
✅ The condition `time > stack[-1]` (strict greater than) correctly handles this — equal times do not push a new fleet.

**Trap 5 — Single car**
One car always forms exactly one fleet. The stack gets one entry → returns 1. Correct.

---

## 27. Largest Rectangle in Histogram

**LeetCode #84 | Difficulty: 🔴 Hard**

### Problem Statement

Given an array of integers `heights` representing a histogram's bar heights, return the area of the largest rectangle in the histogram.

```
Input:  heights = [2,1,5,6,2,3]
Output: 10  (bars at index 2 and 3, height 5, width 2)
```

### Intuition

Use a **monotonic increasing stack** of `(index, height)` pairs. For each bar:
- While the current bar is **shorter** than the stack top → the top bar can no longer extend right. Pop it and compute its maximum rectangle using the current index as the right boundary. The rectangle can extend left as far as the popped bar's stored index (because all bars between are taller).
- Push the current bar with the index of the last popped bar (it can extend left to that position).

After the loop, all remaining bars in the stack extend to the end of the histogram.

### Solution

```python
def largestRectangleArea(heights: list[int]) -> int:
    stack = []  # (start_index, height)
    max_area = 0

    for i, h in enumerate(heights):
        start = i
        while stack and stack[-1][1] > h:
            idx, height = stack.pop()
            max_area = max(max_area, height * (i - idx))
            start = idx  # can extend left to this index
        stack.append((start, h))

    # Remaining bars extend to end
    for idx, height in stack:
        max_area = max(max_area, height * (len(heights) - idx))

    return max_area
```

**Time:** O(n) | **Space:** O(n)

### Interview Traps

**Trap 1 — Brute force O(n²)**
Checking every pair of bars as left and right boundaries is O(n²).  
✅ The monotonic stack achieves O(n).

**Trap 2 — Forgetting to process remaining stack entries**
Bars still in the stack at the end extend all the way to the right boundary of the histogram.  
✅ Always process remaining stack elements after the main loop.

**Trap 3 — Not tracking the extended start index**
When a bar is popped, its rectangle can extend left to where the popped bar originally started (since all intervening bars were taller).  
✅ Set `start = idx` when popping, and use `start` when pushing the current bar.

**Trap 4 — Width calculation**
Width = `i - idx` (current index minus the stored start index of the popped bar), not `i - stack[-1][0]`.

**Trap 5 — Divide and conquer alternative**
A divide-and-conquer approach (find the minimum bar, recurse on both halves) also works in O(n log n) average but O(n²) worst case. The stack approach is strictly superior.

---

*[← Back to Index](./index.md) | [Next: Binary Search →](./05_binary_search.md)*

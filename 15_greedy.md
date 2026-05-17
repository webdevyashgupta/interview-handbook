# Greedy

**Topic 15 of 18 | NeetCode 150 Interview Handbook**  
[← Back to Index](./index.md)

---

## Problems in this Section

| # | Problem | Difficulty |
|---|---------|------------|
| 122 | [Maximum Subarray](#122-maximum-subarray) | 🟡 Medium |
| 123 | [Jump Game](#123-jump-game) | 🟡 Medium |
| 124 | [Jump Game II](#124-jump-game-ii) | 🟡 Medium |
| 125 | [Gas Station](#125-gas-station) | 🟡 Medium |
| 126 | [Hand of Straights](#126-hand-of-straights) | 🟡 Medium |
| 127 | [Merge Triplets to Form Target Triplet](#127-merge-triplets-to-form-target-triplet) | 🟡 Medium |
| 128 | [Partition Labels](#128-partition-labels) | 🟡 Medium |
| 129 | [Valid Parenthesis String](#129-valid-parenthesis-string) | 🟡 Medium |

---

## 122. Maximum Subarray

**LeetCode #53 | Difficulty: 🟡 Medium**

### Problem Statement

Given an integer array `nums`, find the contiguous subarray with the largest sum and return its sum.

```
Input:  nums = [-2,1,-3,4,-1,2,1,-5,4]
Output: 6  ([4,-1,2,1])

Input:  nums = [1]
Output: 1
```

### Intuition

**Kadane's Algorithm**: maintain a running sum `curr`. At each element, decide: extend the current subarray or start fresh. If `curr` becomes negative, it would only hurt future sums — reset to 0. Track the maximum seen.

`curr = max(num, curr + num)` — equivalently, `curr = curr + num; if curr < 0: curr = 0`.

### Solution

```python
def maxSubArray(nums: list[int]) -> int:
    max_sum = nums[0]
    curr = 0

    for num in nums:
        curr += num
        max_sum = max(max_sum, curr)
        if curr < 0:
            curr = 0

    return max_sum
```

**Time:** O(n) | **Space:** O(1)

### Interview Traps

**Trap 1 — Initializing max_sum to 0**
All-negative arrays (e.g., `[-3,-1,-2]`) have max subarray sum = `-1`. Initialize `max_sum = nums[0]`.

**Trap 2 — DP formulation**
`dp[i] = max(nums[i], dp[i-1] + nums[i])` — equivalent to Kadane's, just expressed differently.

**Trap 3 — "Return the actual subarray, not just the sum"**
Track `start`, `end`, and `temp_start` indices. Update them when you find a new max.

**Trap 4 — Divide and conquer alternative**
O(n log n) divide-and-conquer exists (LeetCode mentions it as a follow-up). Kadane's is strictly better.

---

## 123. Jump Game

**LeetCode #55 | Difficulty: 🟡 Medium**

### Problem Statement

Given an array `nums` where `nums[i]` is your max jump length from index `i`, return `true` if you can reach the last index.

```
Input:  nums = [2,3,1,1,4]
Output: true

Input:  nums = [3,2,1,0,4]
Output: false
```

### Intuition

Track the farthest index reachable (`max_reach`) as you iterate. At each index `i`, if `i > max_reach`, it's unreachable — return False. Otherwise, update `max_reach = max(max_reach, i + nums[i])`.

### Solution

```python
def canJump(nums: list[int]) -> bool:
    max_reach = 0

    for i in range(len(nums)):
        if i > max_reach:
            return False
        max_reach = max(max_reach, i + nums[i])

    return True
```

**Time:** O(n) | **Space:** O(1)

### Interview Traps

**Trap 1 — DP approach (O(n²))**
`dp[i] = True` if reachable. For each `i`, check all `j < i`. This is O(n²) and unnecessary.  
✅ Greedy tracking of `max_reach` is O(n).

**Trap 2 — Simulating every possible jump**
Enumerating all jump sequences is exponential. Greedy is the key.

**Trap 3 — Off-by-one at the last index**
You only need to *reach* the last index, not jump *past* it. `i + nums[i] >= last_index` suffices.

**Trap 4 — "What if you want the minimum number of jumps?"**
That's Jump Game II — use BFS-style greedy.

---

## 124. Jump Game II

**LeetCode #45 | Difficulty: 🟡 Medium**

### Problem Statement

Given `nums` where `nums[i]` is your max jump from index `i`, return the minimum number of jumps to reach the last index. You can always reach the last index.

```
Input:  nums = [2,3,1,1,4]
Output: 2  (jump 1→3, then 3→4)

Input:  nums = [2,3,0,1,4]
Output: 2
```

### Intuition

**BFS-style greedy**: treat each "level" as one jump. Track the farthest reachable from the current level (`far`) and the end of the current level (`curr_end`). When you reach `curr_end`, increment jumps and set `curr_end = far`.

### Solution

```python
def jump(nums: list[int]) -> int:
    jumps = 0
    curr_end = 0
    far = 0

    for i in range(len(nums) - 1):  # don't need to jump from last index
        far = max(far, i + nums[i])
        if i == curr_end:
            jumps += 1
            curr_end = far

    return jumps
```

**Time:** O(n) | **Space:** O(1)

### Interview Traps

**Trap 1 — Not stopping at `len(nums) - 1`**
You don't need to jump from the last index. The loop should end at `n-2`.

**Trap 2 — Incrementing jumps inside the loop body (not at level boundary)**
Only increment `jumps` when you hit the boundary of the current level (`i == curr_end`).

**Trap 3 — BFS approach (valid but uses extra space)**
Treat each reachable range as a BFS level. The greedy approach achieves the same in O(1) space.

**Trap 4 — "What if you can't always reach the end?"**
Check if `far < i` at any point (you're stuck). That merges this problem with Jump Game I.

---

## 125. Gas Station

**LeetCode #134 | Difficulty: 🟡 Medium**

### Problem Statement

There are `n` gas stations in a circle. `gas[i]` is the fuel available at station `i`; `cost[i]` is the fuel needed to travel from `i` to `i+1`. Return the starting station index if you can complete the circuit, else `-1`. The answer is unique if it exists.

```
Input:  gas = [1,2,3,4,5], cost = [3,4,5,1,2]
Output: 3
```

### Intuition

**Key insight 1:** If total gas < total cost → impossible. Return -1.

**Key insight 2:** If total gas ≥ total cost, a solution always exists. To find it: simulate the journey keeping a running tank. When the tank goes negative at station `i`, reset to 0 and set start = `i+1`. The remaining segment must be the answer (the total ensures the leftover is enough to cover the deficit).

### Solution

```python
def canCompleteCircuit(gas: list[int], cost: list[int]) -> int:
    if sum(gas) < sum(cost):
        return -1

    tank = 0
    start = 0

    for i in range(len(gas)):
        tank += gas[i] - cost[i]
        if tank < 0:
            tank = 0
            start = i + 1

    return start
```

**Time:** O(n) | **Space:** O(1)

### Interview Traps

**Trap 1 — Brute force O(n²)**
Trying every starting station and simulating is O(n²). The greedy O(n) solution is expected.

**Trap 2 — Not checking total feasibility first**
If `sum(gas) < sum(cost)`, no solution exists. This check enables the greedy correctness argument.

**Trap 3 — "Why is `start = i + 1` always correct?"**
When tank goes negative at `i`, stations `0` through `i` cannot be the start (they'd result in a deficit before reaching `i+1`). The only remaining candidate is `i+1`.

**Trap 4 — Circular indexing**
The single-pass approach naturally handles the circular nature — the greedy doesn't need modular arithmetic.

---

## 126. Hand of Straights

**LeetCode #846 | Difficulty: 🟡 Medium**

### Problem Statement

Given an integer array `hand` and integer `groupSize`, return `true` if the cards can be rearranged into groups of `groupSize` consecutive cards.

```
Input:  hand = [1,2,3,6,2,3,4,7,8], groupSize = 3
Output: true  ([1,2,3],[2,3,4],[6,7,8])

Input:  hand = [1,2,3,4,5], groupSize = 4
Output: false
```

### Intuition

Use a frequency map. Sort the unique keys. For the smallest available card, greedily form a consecutive group starting from it. If any card in the group is missing → return False.

### Solution

```python
from collections import Counter

def isNStraightHand(hand: list[int], groupSize: int) -> bool:
    if len(hand) % groupSize != 0:
        return False

    count = Counter(hand)

    for card in sorted(count):
        if count[card] > 0:
            freq = count[card]
            for i in range(groupSize):
                if count[card + i] < freq:
                    return False
                count[card + i] -= freq

    return True
```

**Time:** O(n log n) | **Space:** O(n)

### Interview Traps

**Trap 1 — Not checking divisibility**
`len(hand) % groupSize != 0` → impossible immediately.

**Trap 2 — Using a sorted list instead of dict**
Sorting the full array repeatedly is expensive. Sort the unique keys of a frequency map.

**Trap 3 — Not using the starting card's full frequency**
If the smallest card appears `freq` times, you need `freq` consecutive groups starting there.

**Trap 4 — "This is the same as 'Divide Array in Sets of K Consecutive Numbers' (LeetCode #1296)"**
Yes — identical problem with different wording. Know both names.

---

## 127. Merge Triplets to Form Target Triplet

**LeetCode #1899 | Difficulty: 🟡 Medium**

### Problem Statement

Given a list of triplets and a target triplet, you can merge triplets by taking the element-wise maximum. Return `true` if you can form the target.

```
Input:  triplets = [[2,5,3],[1,8,4],[1,7,5]], target = [2,7,5]
Output: true  (merge [2,5,3] and [1,7,5])
```

### Intuition

A triplet is **useful** if none of its values exceed the corresponding target value. Unuseful triplets can only increase values beyond the target.

Collect all useful triplets and take element-wise max. If the result equals the target → True.

### Solution

```python
def mergeTriplets(triplets: list[list[int]], target: list[int]) -> bool:
    result = [0, 0, 0]

    for t in triplets:
        if t[0] <= target[0] and t[1] <= target[1] and t[2] <= target[2]:
            result = [max(result[i], t[i]) for i in range(3)]

    return result == target
```

**Time:** O(n) | **Space:** O(1)

### Interview Traps

**Trap 1 — Including triplets that exceed any target dimension**
If any dimension exceeds the target, merging that triplet could push the result over the target value. Strictly filter these out.

**Trap 2 — Thinking you need exact matches**
You don't need a single triplet equal to target — you can combine multiple valid triplets.

**Trap 3 — Order of filtering and merging**
Filter first (only keep valid triplets), then merge. Don't merge first.

---

## 128. Partition Labels

**LeetCode #763 | Difficulty: 🟡 Medium**

### Problem Statement

Partition string `s` into as many parts as possible such that each letter appears in at most one part. Return the list of partition sizes.

```
Input:  s = "ababcbacadefegdehijhklij"
Output: [9,7,8]
```

### Intuition

For each character, track the **last occurrence** index. Greedily extend the current partition as far as the last occurrence of any character seen so far. When the current index equals the end of the partition, finalize it.

### Solution

```python
def partitionLabels(s: str) -> list[int]:
    last = {c: i for i, c in enumerate(s)}  # last occurrence of each char
    result = []
    start = end = 0

    for i, c in enumerate(s):
        end = max(end, last[c])
        if i == end:
            result.append(end - start + 1)
            start = i + 1

    return result
```

**Time:** O(n) | **Space:** O(1) — at most 26 chars

### Interview Traps

**Trap 1 — Using the first occurrence instead of last**
The partition must contain ALL occurrences of each character. Use the **last** occurrence.

**Trap 2 — Not updating end greedily**
As you encounter new characters, extend `end` if their last occurrence is beyond the current `end`.

**Trap 3 — Off-by-one in partition size**
Size = `end - start + 1`. Reset `start = i + 1` after each partition.

**Trap 4 — "Minimum number of partitions" vs "maximize partitions"**
This problem maximizes partitions (greedy). Minimizing would be a different constraint.

---

## 129. Valid Parenthesis String

**LeetCode #678 | Difficulty: 🟡 Medium**

### Problem Statement

Given a string `s` containing `(`, `)`, and `*` (can be `(`, `)`, or empty), return `true` if it's valid.

```
Input:  s = "(*)"
Output: true

Input:  s = "(*))"
Output: true
```

### Intuition

Track a range `[lo, hi]` of possible open-bracket counts:
- `(`: both lo and hi increase by 1
- `)`: both decrease by 1
- `*`: lo decreases (treat as `)` or empty), hi increases (treat as `(`)

If `hi < 0` → too many `)` → return False. Clamp `lo` to 0 (can't have negative opens). If `lo == 0` at the end → valid.

### Solution

```python
def checkValidString(s: str) -> bool:
    lo = hi = 0

    for c in s:
        if c == '(':
            lo += 1
            hi += 1
        elif c == ')':
            lo -= 1
            hi -= 1
        else:  # '*'
            lo -= 1  # treat as ')' or empty
            hi += 1  # treat as '('

        if hi < 0:
            return False  # too many ')'
        lo = max(lo, 0)  # open count can't be negative

    return lo == 0
```

**Time:** O(n) | **Space:** O(1)

### Interview Traps

**Trap 1 — Not clamping lo to 0**
`lo` represents the minimum possible open count. It can't go below 0 (treat `*` as empty or `)` but not below zero opens).

**Trap 2 — Stack-based approach**
Two stacks (one for `(`, one for `*`) — match `)` first against `(`, then against `*`, then fail. Valid if remaining `(` can be matched by `*` in the right order.

**Trap 3 — "What if `*` can only be empty?"**
Then it reduces to the standard Valid Parentheses problem — use a simple counter.

**Trap 4 — DP approach**
`dp[i][j]` = True if `s[0..i-1]` is valid with `j` unmatched `(`. O(n²) — much slower than greedy.

---

*[← Back to Index](./index.md) | [Next: Intervals →](./16_intervals.md)*

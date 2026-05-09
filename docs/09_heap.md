# Heap / Priority Queue

**Topic 9 of 18 | NeetCode 150 Interview Handbook**  
[← Back to Index](./index.md)

---

## Problems in this Section

| # | Problem | Difficulty |
|---|---------|------------|
| 64 | [Kth Largest Element in a Stream](#64-kth-largest-element-in-a-stream) | 🟢 Easy |
| 65 | [Last Stone Weight](#65-last-stone-weight) | 🟢 Easy |
| 66 | [K Closest Points to Origin](#66-k-closest-points-to-origin) | 🟡 Medium |
| 67 | [Kth Largest Element in an Array](#67-kth-largest-element-in-an-array) | 🟡 Medium |
| 68 | [Task Scheduler](#68-task-scheduler) | 🟡 Medium |
| 69 | [Design Twitter](#69-design-twitter) | 🟡 Medium |
| 70 | [Find Median from Data Stream](#70-find-median-from-data-stream) | 🔴 Hard |

---

## 64. Kth Largest Element in a Stream

**LeetCode #703 | Difficulty: 🟢 Easy**

### Problem Statement

Design a class that finds the kth largest element in a stream. The class is initialized with `k` and an initial array `nums`. Each call to `add(val)` adds a value and returns the kth largest element.

```
kthLargest = KthLargest(3, [4,5,8,2])
kthLargest.add(3)  → 4
kthLargest.add(5)  → 5
kthLargest.add(10) → 5
kthLargest.add(9)  → 8
kthLargest.add(4)  → 8
```

### Intuition

Maintain a **min-heap of size k**. The top of the heap is always the kth largest element — everything in the heap is larger than everything outside it.

On `add`: push the new value. If the heap exceeds size k, pop the minimum. The heap top is the kth largest.

### Solution

```python
import heapq

class KthLargest:
    def __init__(self, k: int, nums: list[int]):
        self.k = k
        self.heap = []
        for num in nums:
            self.add(num)

    def add(self, val: int) -> int:
        heapq.heappush(self.heap, val)
        if len(self.heap) > self.k:
            heapq.heappop(self.heap)
        return self.heap[0]
```

**Time:** O(log k) per add | **Space:** O(k)

### Interview Traps

**Trap 1 — Using a max-heap**
A max-heap stores all elements and gives you the largest, not the kth largest.  
✅ Use a min-heap of size k — the minimum of the heap IS the kth largest.

**Trap 2 — Re-sorting on every add**
Sorting after each addition is O(n log n). The heap maintains order in O(log k).

**Trap 3 — Not handling initial nums larger than k**
The constructor should use `add()` to correctly trim the heap down to size k.

**Trap 4 — "What if k > len(nums)?"**
Valid scenario — the heap will have fewer than k elements initially. `add()` calls will grow it.

---

## 65. Last Stone Weight

**LeetCode #1046 | Difficulty: 🟢 Easy**

### Problem Statement

You have a collection of stones with weights. Each turn, smash the two heaviest stones: if equal, both destroyed; if not, the lighter is destroyed and the heavier has weight = difference. Return the weight of the last stone, or 0 if none remain.

```
Input:  stones = [2,7,4,1,8,1]
Output: 1
```

### Intuition

Use a **max-heap** to always access the two heaviest stones in O(log n). Python only has min-heap, so negate values to simulate a max-heap.

### Solution

```python
import heapq

def lastStoneWeight(stones: list[int]) -> int:
    heap = [-s for s in stones]
    heapq.heapify(heap)

    while len(heap) > 1:
        first = -heapq.heappop(heap)
        second = -heapq.heappop(heap)
        if first != second:
            heapq.heappush(heap, -(first - second))

    return -heap[0] if heap else 0
```

**Time:** O(n log n) | **Space:** O(n)

### Interview Traps

**Trap 1 — Not simulating a max-heap in Python**
Python's `heapq` is a min-heap. Negate values to get max-heap behavior.  
✅ Always negate when pushing, negate again when popping.

**Trap 2 — Forgetting to push the remainder**
If `first != second`, push back `first - second` (the remainder). If equal, push nothing.

**Trap 3 — Returning 0 for an empty heap**
If all stones cancel out, the heap is empty. Return 0 in that case.

---

## 66. K Closest Points to Origin

**LeetCode #973 | Difficulty: 🟡 Medium**

### Problem Statement

Given an array of points on a plane, return the k closest points to the origin `(0, 0)`. Distance is Euclidean: `sqrt(x² + y²)`.

```
Input:  points = [[1,3],[-2,2]], k = 1
Output: [[-2,2]]
```

### Intuition

Use a **max-heap of size k** tracking the farthest among the k closest points seen so far. For each new point, if it's closer than the current farthest in the heap, replace it.

Alternatively, sort by distance — simpler but O(n log n) instead of O(n log k).

### Solution

```python
import heapq

def kClosest(points: list[list[int]], k: int) -> list[list[int]]:
    # Max-heap of size k (negate distance for max-heap behavior)
    heap = []
    for x, y in points:
        dist = -(x*x + y*y)  # negated for max-heap
        heapq.heappush(heap, (dist, x, y))
        if len(heap) > k:
            heapq.heappop(heap)
    return [[x, y] for (_, x, y) in heap]
```

**Simple sort approach:**
```python
points.sort(key=lambda p: p[0]**2 + p[1]**2)
return points[:k]
```

**Time:** O(n log k) heap | **Space:** O(k)

### Interview Traps

**Trap 1 — Using sqrt for distance**
`sqrt` is unnecessary — comparing squared distances is equivalent and avoids floating point.  
✅ Use `x*x + y*y` directly.

**Trap 2 — Using a min-heap of all points**
Building a full min-heap is O(n) and extracting k is O(k log n) — acceptable but wastes space storing all n points.  
✅ Max-heap of size k is O(n log k) time and O(k) space.

**Trap 3 — "What if k == n?"**
Return all points. The heap and sort approaches both handle this.

**Trap 4 — Quickselect O(n) average**
Quickselect can find the k closest in O(n) average time. Know it exists as an advanced optimization.

---

## 67. Kth Largest Element in an Array

**LeetCode #215 | Difficulty: 🟡 Medium**

### Problem Statement

Given an integer array `nums` and integer `k`, return the kth largest element (not kth largest distinct).

```
Input:  nums = [3,2,1,5,6,4], k = 2
Output: 5
```

### Intuition

**Option 1: Min-heap of size k** — O(n log k)  
**Option 2: Quickselect** — O(n) average, O(n²) worst case  
**Option 3: Sort** — O(n log n), simplest

Quickselect is the optimal average-case approach: partition like QuickSort, but only recurse into the side containing the kth element.

### Solution

**Min-heap approach:**
```python
import heapq

def findKthLargest(nums: list[int], k: int) -> int:
    return heapq.nlargest(k, nums)[-1]
```

**Quickselect approach (optimal):**
```python
import random

def findKthLargest(nums: list[int], k: int) -> int:
    target = len(nums) - k  # kth largest = target-th smallest (0-indexed)

    def quickselect(left, right):
        pivot = nums[right]
        p = left
        for i in range(left, right):
            if nums[i] <= pivot:
                nums[i], nums[p] = nums[p], nums[i]
                p += 1
        nums[p], nums[right] = nums[right], nums[p]

        if p == target:
            return nums[p]
        elif p < target:
            return quickselect(p + 1, right)
        else:
            return quickselect(left, p - 1)

    random.shuffle(nums)  # avoid worst-case O(n²)
    return quickselect(0, len(nums) - 1)
```

**Time:** O(n) average, O(n²) worst | **Space:** O(1)

### Interview Traps

**Trap 1 — Sorting as the final answer**
O(n log n) sorting works but isn't optimal. The interviewer may push for O(n).

**Trap 2 — Not randomizing pivot in Quickselect**
Without randomization, sorted/reverse-sorted input degrades to O(n²).  
✅ Always shuffle or use random pivot.

**Trap 3 — Confusing kth largest with kth smallest**
kth largest of n elements = (n-k)th smallest (0-indexed).  
✅ `target = len(nums) - k` in the Quickselect.

**Trap 4 — "Can you guarantee O(n) worst case?"**
Use median-of-medians algorithm — O(n) worst case but complex. Know it exists; the randomized version is preferred in practice.

---

## 68. Task Scheduler

**LeetCode #621 | Difficulty: 🟡 Medium**

### Problem Statement

Given a list of CPU tasks `tasks` and a non-negative integer `n` (cooldown between same tasks), return the minimum number of intervals the CPU needs to finish all tasks.

```
Input:  tasks = ["A","A","A","B","B","B"], n = 2
Output: 8  (A→B→idle→A→B→idle→A→B)
```

### Intuition

The task with the **highest frequency** drives the minimum time. Place it first, then fill cooldown slots with other tasks. The formula:

`min_time = max(len(tasks), (max_freq - 1) * (n + 1) + count_of_max_freq_tasks)`

Alternatively, simulate with a max-heap and a queue tracking cooldown times.

### Solution

**Formula approach:**
```python
from collections import Counter

def leastInterval(tasks: list[str], n: int) -> int:
    freq = Counter(tasks)
    max_freq = max(freq.values())
    count_max = sum(1 for f in freq.values() if f == max_freq)
    return max(len(tasks), (max_freq - 1) * (n + 1) + count_max)
```

**Heap + Queue simulation:**
```python
import heapq
from collections import Counter, deque

def leastInterval(tasks: list[str], n: int) -> int:
    freq = Counter(tasks)
    heap = [-f for f in freq.values()]
    heapq.heapify(heap)

    time = 0
    cooldown = deque()  # (available_at, freq)

    while heap or cooldown:
        time += 1
        if heap:
            f = heapq.heappop(heap) + 1  # use one instance
            if f < 0:
                cooldown.append((time + n, f))
        if cooldown and cooldown[0][0] == time:
            heapq.heappush(heap, cooldown.popleft()[1])

    return time
```

**Time:** O(n log 26) = O(n) | **Space:** O(26) = O(1)

### Interview Traps

**Trap 1 — Not accounting for idle slots**
The minimum time can exceed `len(tasks)` if the cooldown forces idle slots.  
✅ `max(len(tasks), formula)` handles both cases.

**Trap 2 — Over-complicating without the formula**
The formula is elegant and O(1) — know it. The heap simulation is useful for understanding but is more complex.

**Trap 3 — "What if n = 0?"**
No cooldown → answer is just `len(tasks)`. Both approaches handle this.

**Trap 4 — Counting tasks with max frequency**
Multiple tasks tied for max frequency all contribute to the last "chunk". `count_max` accounts for this.

---

## 69. Design Twitter

**LeetCode #355 | Difficulty: 🟡 Medium**

### Problem Statement

Design a simplified version of Twitter with:
- `postTweet(userId, tweetId)` — post a tweet
- `getNewsFeed(userId)` — return the 10 most recent tweets from the user and their followees
- `follow(followerId, followeeId)` — follow a user
- `unfollow(followerId, followeeId)` — unfollow a user

### Intuition

- Store tweets as `(timestamp, tweetId)` lists per user (most recent first).
- For `getNewsFeed`, merge the tweet lists of the user and all followees using a **max-heap** keyed by timestamp — same technique as Merge K Sorted Lists.

### Solution

```python
import heapq
from collections import defaultdict

class Twitter:
    def __init__(self):
        self.time = 0
        self.tweets = defaultdict(list)   # userId -> [(time, tweetId)]
        self.following = defaultdict(set) # userId -> set of followeeIds

    def postTweet(self, userId: int, tweetId: int) -> None:
        self.tweets[userId].append((self.time, tweetId))
        self.time -= 1  # decreasing so min-heap acts as max-heap

    def getNewsFeed(self, userId: int) -> list[int]:
        heap = []
        users = self.following[userId] | {userId}

        for user in users:
            tweets = self.tweets[user]
            if tweets:
                # Push (time, tweetId, user, tweet_index)
                idx = len(tweets) - 1
                t, tid = tweets[idx]
                heapq.heappush(heap, (t, tid, user, idx - 1))

        result = []
        while heap and len(result) < 10:
            t, tid, user, idx = heapq.heappop(heap)
            result.append(tid)
            if idx >= 0:
                t2, tid2 = self.tweets[user][idx]
                heapq.heappush(heap, (t2, tid2, user, idx - 1))

        return result

    def follow(self, followerId: int, followeeId: int) -> None:
        self.following[followerId].add(followeeId)

    def unfollow(self, followerId: int, followeeId: int) -> None:
        self.following[followerId].discard(followeeId)
```

**Time:** O(k log u) for getNewsFeed, k=10, u=users followed | **Space:** O(tweets + follows)

### Interview Traps

**Trap 1 — Collecting all tweets and sorting**
Collecting every tweet and sorting is O(n log n) — wasteful.  
✅ Use a heap to merge k sorted lists in O(k log u).

**Trap 2 — Not including the user's own tweets**
`getNewsFeed` includes the user's own tweets plus their followees'.

**Trap 3 — Using disccard vs remove for unfollow**
`set.remove()` raises KeyError if the element doesn't exist. Use `set.discard()` for safe removal.

**Trap 4 — Timestamp management**
Use a global counter (decrement for min-heap max behavior) to establish tweet ordering.

---

## 70. Find Median from Data Stream

**LeetCode #295 | Difficulty: 🔴 Hard**

### Problem Statement

Design a data structure that supports adding integers from a stream and finding the median at any point.

```
MedianFinder mf = new MedianFinder()
mf.addNum(1)
mf.addNum(2)
mf.findMedian() → 1.5
mf.addNum(3)
mf.findMedian() → 2.0
```

### Intuition

Maintain two heaps:
- **Max-heap** (`small`) for the lower half of numbers.
- **Min-heap** (`large`) for the upper half of numbers.

Keep them balanced (sizes differ by at most 1). The median is either:
- The top of `small` (odd total, small is larger), or
- The average of both tops (even total).

### Solution

```python
import heapq

class MedianFinder:
    def __init__(self):
        self.small = []  # max-heap (negated) — lower half
        self.large = []  # min-heap — upper half

    def addNum(self, num: int) -> None:
        # Always push to small first
        heapq.heappush(self.small, -num)

        # Balance: small's max must be <= large's min
        if self.small and self.large and (-self.small[0]) > self.large[0]:
            heapq.heappush(self.large, -heapq.heappop(self.small))

        # Rebalance sizes (small can be at most 1 larger)
        if len(self.small) > len(self.large) + 1:
            heapq.heappush(self.large, -heapq.heappop(self.small))
        if len(self.large) > len(self.small):
            heapq.heappush(self.small, -heapq.heappop(self.large))

    def findMedian(self) -> float:
        if len(self.small) > len(self.large):
            return -self.small[0]
        return (-self.small[0] + self.large[0]) / 2.0
```

**Time:** O(log n) addNum, O(1) findMedian | **Space:** O(n)

### Interview Traps

**Trap 1 — Not maintaining heap ordering between halves**
If `small`'s max > `large`'s min, the partition is wrong. Always rebalance after insertion.

**Trap 2 — Off-by-one in size balancing**
`small` can be at most 1 larger than `large` (for odd-count median). If `large` grows larger than `small`, rebalance immediately.

**Trap 3 — Python min-heap for small (lower half)**
The lower half needs a max-heap. Negate values when pushing to `small`.

**Trap 4 — "What if the stream has many duplicates?"**
The two-heap approach handles duplicates naturally — no special casing needed.

**Trap 5 — Follow-up: what if values are in [0, 100]?**
Use a bucket/count array of size 101 and maintain a running count. O(1) insert and O(1) median with prefix sums.

---

*[← Back to Index](./index.md) | [Next: Backtracking →](./10_backtracking.md)*

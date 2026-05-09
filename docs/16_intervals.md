# Intervals

**Topic 16 of 18 | NeetCode 150 Interview Handbook**  
[← Back to Index](./index.md)

---

## Problems in this Section

| # | Problem | Difficulty |
|---|---------|------------|
| 130 | [Insert Interval](#130-insert-interval) | 🟡 Medium |
| 131 | [Merge Intervals](#131-merge-intervals) | 🟡 Medium |
| 132 | [Non-Overlapping Intervals](#132-non-overlapping-intervals) | 🟡 Medium |
| 133 | [Meeting Rooms](#133-meeting-rooms) | 🟢 Easy |
| 134 | [Meeting Rooms II](#134-meeting-rooms-ii) | 🟡 Medium |
| 135 | [Minimum Interval to Include Each Query](#135-minimum-interval-to-include-each-query) | 🔴 Hard |

---

## 130. Insert Interval

**LeetCode #57 | Difficulty: 🟡 Medium**

### Problem Statement

Given a sorted list of non-overlapping intervals and a new interval, insert the new interval and merge any overlapping intervals.

```
Input:  intervals = [[1,3],[6,9]], newInterval = [2,5]
Output: [[1,5],[6,9]]

Input:  intervals = [[1,2],[3,5],[6,7],[8,10],[12,16]], newInterval = [4,8]
Output: [[1,2],[3,10],[12,16]]
```

### Intuition

Three phases:
1. Add all intervals that end **before** the new interval starts.
2. Merge all overlapping intervals into the new interval.
3. Add all remaining intervals that start **after** the merged interval ends.

### Solution

```python
def insert(intervals: list[list[int]], newInterval: list[int]) -> list[list[int]]:
    result = []
    i = 0
    n = len(intervals)

    # Phase 1: Add all non-overlapping intervals before newInterval
    while i < n and intervals[i][1] < newInterval[0]:
        result.append(intervals[i])
        i += 1

    # Phase 2: Merge all overlapping intervals
    while i < n and intervals[i][0] <= newInterval[1]:
        newInterval[0] = min(newInterval[0], intervals[i][0])
        newInterval[1] = max(newInterval[1], intervals[i][1])
        i += 1
    result.append(newInterval)

    # Phase 3: Add remaining intervals
    while i < n:
        result.append(intervals[i])
        i += 1

    return result
```

**Time:** O(n) | **Space:** O(n)

### Interview Traps

**Trap 1 — Wrong overlap condition**
Two intervals `[a,b]` and `[c,d]` overlap iff `c <= b` (start of second ≤ end of first).  
✅ Merge phase: `intervals[i][0] <= newInterval[1]`.

**Trap 2 — Sorting when not needed**
The input is already sorted. Don't sort again — it's O(n log n) overhead.

**Trap 3 — Mutating newInterval vs creating new**
Mutating `newInterval` in place is fine since we're building the merge inline.

**Trap 4 — Off-by-one in phase boundary conditions**
Phase 1 ends when `intervals[i][1] < newInterval[0]` (strictly less — touching intervals don't overlap). Phase 2 ends when `intervals[i][0] > newInterval[1]`.

---

## 131. Merge Intervals

**LeetCode #56 | Difficulty: 🟡 Medium**

### Problem Statement

Given a list of intervals, merge all overlapping intervals and return the resulting list.

```
Input:  intervals = [[1,3],[2,6],[8,10],[15,18]]
Output: [[1,6],[8,10],[15,18]]

Input:  intervals = [[1,4],[4,5]]
Output: [[1,5]]
```

### Intuition

Sort by start time. Iterate and for each interval, either merge with the last interval in the result (if overlapping) or append it.

Two intervals overlap if the current start ≤ previous end.

### Solution

```python
def merge(intervals: list[list[int]]) -> list[list[int]]:
    intervals.sort(key=lambda x: x[0])
    result = [intervals[0]]

    for start, end in intervals[1:]:
        if start <= result[-1][1]:
            result[-1][1] = max(result[-1][1], end)  # merge
        else:
            result.append([start, end])

    return result
```

**Time:** O(n log n) | **Space:** O(n)

### Interview Traps

**Trap 1 — Not sorting first**
Without sorting, you might miss intervals that should be merged with earlier ones.  
✅ Always sort by start time first.

**Trap 2 — Wrong overlap condition (using strict inequality)**
`[1,4]` and `[4,5]` share endpoint 4 — they do overlap. Use `<=`, not `<`.

**Trap 3 — Not taking max of end when merging**
If a new interval is completely contained within the previous, `end` might be smaller. Always `max(result[-1][1], end)`.

**Trap 4 — "What if intervals are given in any order?"**
Sort first — the algorithm handles arbitrary input order after sorting.

---

## 132. Non-Overlapping Intervals

**LeetCode #435 | Difficulty: 🟡 Medium**

### Problem Statement

Given an array of intervals, return the minimum number of intervals to remove to make the rest non-overlapping.

```
Input:  intervals = [[1,2],[2,3],[3,4],[1,3]]
Output: 1  (remove [1,3])

Input:  intervals = [[1,2],[1,2],[1,2]]
Output: 2
```

### Intuition

**Greedy**: sort by end time. Keep intervals that don't overlap with the last kept interval. When there's a conflict, remove the one with the later end time (keep the earlier-ending one to maximize space for future intervals).

Count of removals = total - count of kept intervals.

### Solution

```python
def eraseOverlapIntervals(intervals: list[list[int]]) -> int:
    intervals.sort(key=lambda x: x[1])  # sort by end time
    kept = 0
    prev_end = float('-inf')

    for start, end in intervals:
        if start >= prev_end:  # no overlap
            kept += 1
            prev_end = end

    return len(intervals) - kept
```

**Time:** O(n log n) | **Space:** O(1)

### Interview Traps

**Trap 1 — Sorting by start time instead of end time**
Sorting by end time is the key greedy insight — always keep the interval that ends earliest to leave the most room for future intervals.

**Trap 2 — Counting removals directly instead of kept intervals**
It's easier to count kept intervals and subtract — avoids off-by-one errors.

**Trap 3 — Using strict vs non-strict overlap**
`[1,2]` and `[2,3]` — touching at 2 — are NOT overlapping. Use `start >= prev_end` (non-strict).

**Trap 4 — "This is equivalent to finding the maximum number of non-overlapping intervals"**
Yes — this is the Activity Selection Problem. The greedy (sort by end, keep non-overlapping) is classic.

---

## 133. Meeting Rooms

**LeetCode #252 | Difficulty: 🟢 Easy**

### Problem Statement

Given an array of meeting time intervals `[start, end]`, determine if a person can attend all meetings (no overlap).

```
Input:  intervals = [[0,30],[5,10],[15,20]]
Output: false

Input:  intervals = [[7,10],[2,4]]
Output: true
```

### Intuition

Sort by start time. Check if any meeting starts before the previous one ends.

### Solution

```python
def canAttendMeetings(intervals: list[list[int]]) -> bool:
    intervals.sort(key=lambda x: x[0])

    for i in range(1, len(intervals)):
        if intervals[i][0] < intervals[i-1][1]:
            return False

    return True
```

**Time:** O(n log n) | **Space:** O(1)

### Interview Traps

**Trap 1 — Not sorting first**
Without sorting, adjacent pairs in the original order may not actually be the ones that conflict.

**Trap 2 — Using `<=` in the overlap check**
`[0,5]` and `[5,10]` — one ends exactly when the next starts — are NOT overlapping (no conflict).  
✅ Use strict `<`.

**Trap 3 — "How many rooms are needed?" (Meeting Rooms II)**
That's the next problem. This one only asks if ONE person can attend all — a simpler check.

---

## 134. Meeting Rooms II

**LeetCode #253 | Difficulty: 🟡 Medium**

### Problem Statement

Given meeting time intervals, return the minimum number of conference rooms required.

```
Input:  intervals = [[0,30],[5,10],[15,20]]
Output: 2

Input:  intervals = [[7,10],[2,4]]
Output: 1
```

### Intuition

**Min-heap approach**: sort by start time. Use a min-heap of end times (rooms in use). For each new meeting, if the earliest-ending room is free (its end ≤ new start), reuse it. Otherwise, add a new room.

**Two-pointer approach**: separate and sort start times and end times. Use two pointers to count concurrent meetings.

### Solution

**Min-heap approach:**
```python
import heapq

def minMeetingRooms(intervals: list[list[int]]) -> int:
    intervals.sort(key=lambda x: x[0])
    heap = []  # min-heap of end times

    for start, end in intervals:
        if heap and heap[0] <= start:
            heapq.heapreplace(heap, end)
        else:
            heapq.heappush(heap, end)

    return len(heap)
```

**Two-pointer approach:**
```python
def minMeetingRooms(intervals: list[list[int]]) -> int:
    starts = sorted(s for s, e in intervals)
    ends = sorted(e for s, e in intervals)

    rooms = 0
    j = 0
    for i in range(len(starts)):
        if starts[i] < ends[j]:
            rooms += 1
        else:
            j += 1

    return rooms
```

**Time:** O(n log n) | **Space:** O(n)

### Interview Traps

**Trap 1 — Using a max-heap instead of min-heap**
You need to check the earliest-ending room (smallest end time) to see if it's free. Min-heap is correct.

**Trap 2 — Sorting by end time in two-pointer approach**
Sort starts and ends independently — they come from different meetings.

**Trap 3 — Off-by-one in two-pointer**
Use strict `<` for overlap: `starts[i] < ends[j]` means a new room is needed.

**Trap 4 — "Why does two-pointer work?"**
At any point in time, the number of rooms = number of meetings that have started - number that have finished. The two-pointer simulates this count efficiently.

---

## 135. Minimum Interval to Include Each Query

**LeetCode #2406 | Difficulty: 🔴 Hard**

### Problem Statement

Given intervals and queries, for each query return the size of the smallest interval that contains the query point. Return -1 if no interval contains it.

```
Input:  intervals = [[1,4],[2,4],[3,6],[4,4]], queries = [2,3,4,5]
Output: [3,3,1,4]
```

### Intuition

Sort both intervals and queries. Use a min-heap keyed by interval size. For each query (processed in sorted order):
1. Add all intervals whose start ≤ query to the heap.
2. Remove intervals from the heap whose end < query.
3. The top of the heap is the smallest valid interval.

Since queries are processed out of original order, map results back using original indices.

### Solution

```python
import heapq

def minInterval(intervals: list[list[int]], queries: list[int]) -> list[int]:
    intervals.sort()
    sorted_queries = sorted(enumerate(queries), key=lambda x: x[1])
    result = [-1] * len(queries)

    heap = []  # (size, end)
    i = 0

    for orig_idx, q in sorted_queries:
        # Add all intervals starting at or before q
        while i < len(intervals) and intervals[i][0] <= q:
            start, end = intervals[i]
            heapq.heappush(heap, (end - start + 1, end))
            i += 1

        # Remove intervals that have ended before q
        while heap and heap[0][1] < q:
            heapq.heappop(heap)

        if heap:
            result[orig_idx] = heap[0][0]

    return result
```

**Time:** O((n + m) log n) where n = intervals, m = queries | **Space:** O(n + m)

### Interview Traps

**Trap 1 — Processing queries in original order**
Sorting queries allows a clean sweep-line approach. Processing in original order requires re-scanning intervals for each query.

**Trap 2 — Not restoring original query order**
Use `enumerate` to track original indices and map results back.

**Trap 3 — Heap keyed by end, not size**
Key the heap by `size` (primary) so the smallest interval is always on top.

**Trap 4 — Not removing expired intervals**
Intervals whose end < query point cannot contain the query. Remove them to avoid incorrect answers.

---

*[← Back to Index](./index.md) | [Next: Math & Geometry →](./17_math_geometry.md)*

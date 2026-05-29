# Merge Overlapping Intervals

## Problem Summary
Given an array of intervals where `intervals[i] = [start_i, end_i]`, merge all overlapping intervals and return an array of the non-overlapping intervals that cover all the intervals in the input.

## Intuition
If we sort the intervals based on their start times, overlapping intervals will appear adjacent to each other. This allows us to merge them in a single pass.

## Approach

### 1. Brute Force
Sort the intervals and then for each interval, try to merge it with all subsequent intervals.
- **Steps:**
    1. Sort the intervals based on start times.
    2. Iterate through each interval `i`.
    3. If `intervals[i]` is already part of a merged interval, skip it.
    4. Otherwise, start a new merged interval `[start, end]` from `intervals[i]`.
    5. Check all subsequent intervals `j`. If `intervals[j].start <= end`, update `end = max(end, intervals[j].end)`.
    6. Add the merged interval to the result.
- **Time Complexity:** $O(N \log N)$ for sorting + $O(N^2)$ for the nested loops.
- **Space Complexity:** $O(N)$ for storing results.

### 2. Optimal Approach (Single Pass)
Sort the intervals and merge them as you iterate.
- **Steps:**
    1. Sort the intervals based on start times.
    2. Initialize an empty list `merged`.
    3. Iterate through the sorted intervals:
        - If `merged` is empty or the current interval's start is greater than the last merged interval's end (`current[0] > merged.back()[1]`):
            - Add the current interval to `merged`.
        - Otherwise (there is an overlap):
            - Update the last merged interval's end to be the maximum of its current end and the current interval's end: `merged.back()[1] = max(merged.back()[1], current[1])`.
- **Time Complexity:** $O(N \log N)$ for sorting + $O(N)$ for the linear scan.
- **Space Complexity:** $O(N)$ for the result list.

## Complexity
- **Time Complexity:** $O(N \log N)$ due to sorting.
- **Space Complexity:** $O(N)$ for the output list. If we don't count the output space, it is $O(1)$ (or $O(N)$ depending on sorting algorithm implementation).

## Pattern
- **Sorting + Greedy**: Sorting helps in making local decisions (merging adjacent intervals) that lead to a global solution.

## Common Mistakes
- **Not sorting**: If the intervals are not sorted, you cannot easily identify overlapping ones.
- **Wrong overlap condition**: Forgetting to use `max()` for the end time when merging. Example: `[1, 5]` and `[2, 4]` should merge to `[1, 5]`, not `[1, 4]`.

## Related Problems
- Insert Interval
- Non-overlapping Intervals
- Meeting Rooms
- Meeting Rooms II

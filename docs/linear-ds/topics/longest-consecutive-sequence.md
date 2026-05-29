# Longest Consecutive Sequence

## Problem Summary
Given an unsorted array of integers `nums`, return the length of the longest consecutive elements sequence. The sequence must consist of integers that can be arranged such that each element is exactly one greater than the previous element (e.g., [1, 2, 3, 4]).

---

## Intuition
The core idea is to identify the starting element of any potential consecutive sequence. Once a starting element is found, we can easily count how far the sequence extends.

---

## Approach

### Brute Force
1. For every element `x` in the array, search for `x + 1`, then `x + 2`, and so on, using linear search on the original array.
2. Maintain a `count` for the current sequence and update the `longest` sequence found so far.
- **Time Complexity:** $O(N^2)$
- **Space Complexity:** $O(1)$

### Better Approach (Sorting)
1. Sort the array.
2. Iterate through the sorted array and keep track of the `current_count` of consecutive elements.
3. If the current element is `previous + 1`, increment `current_count`.
4. If the current element is the same as the previous, skip it (handle duplicates).
5. If the current element is not `previous + 1`, reset `current_count` to 1.
- **Time Complexity:** $O(N \log N)$ due to sorting.
- **Space Complexity:** $O(1)$ or $O(N)$ depending on the sorting implementation.

### Optimal Approach (Hash Set)
1. Insert all array elements into an **Unordered Set** (or Hash Set).
2. Iterate through the elements of the set.
3. For each element `x`, check if `x - 1` exists in the set:
   - If `x - 1` **exists**, then `x` is not the start of a sequence. Skip it.
   - If `x - 1` **does not exist**, then `x` is the start of a sequence.
4. From the starting element `x`, keep searching for `x + 1`, `x + 2`, etc., in the set until the sequence breaks.
5. Update the `longest` sequence length.
- **Time Complexity:** $O(N)$ because each element is visited at most twice.
- **Space Complexity:** $O(N)$ to store elements in the set.

---

## Complexity
- **Time Complexity:** $O(N)$ (Average case for Hash Set operations).
- **Space Complexity:** $O(N)$ for the Hash Set.

---

## Pattern
- Hash Set for $O(1)$ lookups.
- Sequence Start Identification (checking `x-1`).

---

## Common Mistakes
- **Redundant Work:** Not checking for `x-1`, which causes the algorithm to perform $O(N^2)$ work in the worst case (e.g., for a perfectly consecutive array).
- **Duplicate Handling:** Failing to account for duplicate numbers in the input array.
- **Set Choice:** Using a ordered set (TreeSet) which would make the complexity $O(N \log N)$ instead of $O(N)$.

---

## Related Problems
- Longest Increasing Subsequence
- Find Missing Positive
- Binary Tree Longest Consecutive Sequence

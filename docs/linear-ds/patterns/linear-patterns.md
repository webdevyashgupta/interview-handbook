# Linear Data Structures Patterns

This guide categorizes common patterns found in Arrays, Strings, Stacks, Queues, and Linked Lists. Use this to identify which technique to apply when faced with a new problem.

---

## 1. Basic Array Techniques
Fundamental operations involving single passes, sorting, or basic frequency counting.

*   **Description:** Handling basic transformations, rotations, and simple statistics in linear time or space.
*   **पहचान (How to Identify):** 
    *   Finding the largest/smallest elements.
    *   Removing duplicates or moving specific elements (e.g., zeros) to one end.
    *   Simple array manipulations that don't require complex data structures.
*   **Relevant Topics:**
    *   [Basics: Largest, Second Largest, Duplicates](../topics/arrays-basics-largest-second-largest-duplicates.md)
    *   [Rotations & Move Zeros](../topics/arrays-rotate-move-zeros-union-intersection.md)
    *   [Leaders in an Array](../topics/leaders-in-an-array.md)
    *   [Sort 0s, 1s, 2s (Dutch National Flag)](../topics/sort-an-array-of-0s-1s-2s.md)
    *   [Next Permutation](../topics/next-permutation.md)
    *   [Kadane's Algorithm (Max Subarray Sum)](../topics/kadanes-algorithm.md)
    *   [Majority Element (Moore's Voting)](../topics/majority-element-i.md)

---

## 2. Two Pointers
Using two indices to traverse the data structure, either from the same side or opposite sides.

*   **Description:** Optimizing $O(N^2)$ nested loops to $O(N)$ by maintaining two pointers that move based on conditions.
*   **पहचान (How to Identify):** 
    *   Input is a **sorted** array.
    *   Finding pairs or triplets that sum to a target.
    *   Comparing elements from both ends (e.g., Palindrome, Trapping Rainwater).
*   **Relevant Topics:**
    *   [Two Sum](../topics/arrays-two-sum.md)
    *   [3-Sum Problem](../topics/3-sum.md)
    *   [4-Sum Problem](../topics/4-sum.md)
    *   [Trapping Rainwater](../topics/trapping-rainwater.md)
    *   [Reverse Pairs](../topics/reverse-pairs.md)

---

## 3. Sliding Window
Maintaining a continuous "window" of elements to find subarrays satisfying a specific condition.

*   **Description:** Efficiently tracking a subset of elements as the window moves across the array.
*   **पहचान (How to Identify):** 
    *   Problem asks for the **Longest/Shortest/Maximum/Minimum** subarray or substring.
    *   Constraints involve "exactly K", "at most K", or "sum equals K".
*   **Relevant Topics:**
    *   [Sliding Window Introduction](../topics/sliding-window-introduction.md)
    *   [Maximum Points from Cards](../topics/maximum-points-you-can-obtain-from-cards.md)
    *   [Longest Substring Without Repeating Characters](../topics/longest-substring-without-repeating-characters.md)
    *   [Max Consecutive Ones III](../topics/max-consecutive-ones-iii.md)
    *   [Fruit Into Baskets](../topics/fruit-into-baskets.md)
    *   [Minimum Window Substring](../topics/minimum-window-substring.md)
    *   [Subarray with K Different Integers](../topics/subarray-with-k-different-integers.md)

---

## 4. Prefix Sum / Hashing
Pre-calculating cumulative data or using HashMaps for $O(1)$ lookups.

*   **Description:** Storing state (sum, frequency, index) to answer range queries or existence checks instantly.
*   **पहचान (How to Identify):** 
    *   Frequent range sum queries.
    *   Finding subarrays with a specific sum or XOR.
    *   Problems requiring you to remember if a value was seen before.
*   **Relevant Topics:**
    *   [Longest Subarray with Sum K](../topics/arrays-longest-subarray-sum-k.md)
    *   [Count Subarrays with Sum K](../topics/count-subarray-sum-equals-k.md)
    *   [Subarrays with XOR K](../topics/number-of-subarrays-with-xor-k.md)
    *   [Longest Consecutive Sequence](../topics/longest-consecutive-sequence.md)
    *   [Merge Overlapping Intervals](../topics/merge-overlapping-intervals.md)

---

## 5. Matrix Traversals
Navigating 2D arrays using specific geometric patterns.

*   **Description:** Logic for visiting cells in a matrix, often involving row/column manipulations.
*   **पहचान (How to Identify):** 
    *   Rotating an image/matrix.
    *   Traversing in a Spiral or Zig-zag manner.
    *   Modifying rows/columns based on specific cell triggers (e.g., Set Zeroes).
*   **Relevant Topics:**
    *   [Set Matrix Zeroes](../topics/set-matrix-zeroes.md)
    *   [Rotate Matrix by 90 Degrees](../topics/rotate-matrix-image-by-90-degrees.md)
    *   [Spiral Matrix Traversal](../topics/spiral-matrix.md)

---

## 6. Monotonic Stack
Using a stack to maintain elements in a strictly increasing or decreasing order.

*   **Description:** Efficiently finding the "Next Greater" or "Previous Smaller" element for every item in an array.
*   **पहचान (How to Identify):** 
    *   Finding the nearest larger/smaller neighbor.
    *   Calculating areas under a histogram.
    *   Range-based problems where the current element's contribution depends on its neighbors.
*   **Relevant Topics:**
    *   [Next Greater Element](../topics/next-greater-element.md)
    *   [Next Greater Element II (Circular)](../topics/next-greater-element-ii.md)
    *   [Previous Smaller Element](../topics/previous-smaller-element.md)
    *   [Largest Rectangle in Histogram](../topics/largest-rectangle-in-histogram.md)
    *   [Maximal Rectangle in Binary Matrix](../topics/maximal-rectangle.md)
    *   [Stock Span Problem](../topics/stock-span-problem.md)
    *   [Sum of Subarray Minimums](../topics/sum-of-subarray-minimums.md)

---

## 7. Monotonic Deque
Maintaining a double-ended queue for range-based extremums.

*   **Description:** Keeping track of the maximum or minimum element in a sliding window of size $K$.
*   **पहचान (How to Identify):** 
    *   "Find the maximum in every sliding window of size K".
    *   Need to efficiently add/remove elements from both ends while keeping the extremum at the front.
*   **Relevant Topics:**
    *   [Sliding Window Maximum](../topics/sliding-window-maximum.md)

---

## 8. Cache Design
Implementing data structures with custom eviction policies.

*   **Description:** Combining HashMaps with Doubly Linked Lists to achieve $O(1)$ time complexity for access and updates.
*   **पहचान (How to Identify):** 
    *   System design problems involving "Least Recently Used" or "Least Frequently Used".
    *   Fixed capacity storage with specific removal rules.
*   **Relevant Topics:**
    *   [LRU Cache Implementation](../topics/lru-cache.md)
    *   [LFU Cache Implementation](../topics/lfu-cache.md)

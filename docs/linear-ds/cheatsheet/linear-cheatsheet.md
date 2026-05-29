# Linear Data Structures Cheatsheet

## Complexity Table
| Data Structure | Access | Search | Insertion | Deletion | Notes |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Array** | $O(1)$ | $O(n)$ | $O(n)$ | $O(n)$ | $O(1)$ push/pop at end. |
| **Stack** | $O(n)$ | $O(n)$ | $O(1)$ | $O(1)$ | LIFO (Push/Pop/Top). |
| **Queue** | $O(n)$ | $O(n)$ | $O(1)$ | $O(1)$ | FIFO (Enqueue/Dequeue). |
| **Deque** | $O(1)$ | $O(n)$ | $O(1)$ | $O(1)$ | Double-ended (ArrayDeque). |
| **Hash Table**| N/A | $O(1)$* | $O(1)$* | $O(1)$* | *Average case; $O(n)$ worst. |

---

## When to Use What
| Pattern | Use Case |
| :--- | :--- |
| **Two Pointers (Opposite)** | 2-Sum (Sorted), Reversing, Palindrome, Container with Most Water. |
| **Two Pointers (Same)** | Sliding Window (Subarrays), Remove Duplicates, Move Zeros. |
| **Sliding Window** | Max/Min sum subarray of size K, Longest substring with K distinct. |
| **Prefix Sum** | Subarray sum equals K, Range Sum Queries, XOR queries. |
| **Monotonic Stack** | Next Greater/Smaller Element, Stock Span, Histogram Area. |
| **Monotonic Deque** | Sliding Window Maximum. |
| **Dutch National Flag** | Sort 0s, 1s, 2s (3-way partition). |
| **Moore’s Voting** | Majority Element (> n/2). |
| **Kadane’s Algo** | Maximum Subarray Sum. |

---

## Common Pitfalls

### Sliding Window & Two Pointers
- **Window Size:** Remember window length is `right - left + 1`.
- **Shrinking Logic:** Ensure the `while` loop for shrinking `left` correctly resets the state (e.g., decrementing counts).
- **Exactly K vs At Most K:** "Exactly K" problems are often solved as `atMost(K) - atMost(K-1)`.
- **Sorting:** Two-pointer pair finding requires a sorted array; don't forget `Arrays.sort()`.
- **Overflow:** When calculating `mid` or sums, use `long` if values exceed $10^9$.

---

## Space Optimization: Matrices
- **In-place Marking:** Use the first row and first column to store "zero" state for the rest of the matrix (e.g., *Set Matrix Zeroes*).
- **Single Variable for Col0:** When using row/col as markers, use one extra variable for `matrix[0][0]` to distinguish between row0 and col0 being zeroed.
- **Layer-by-Layer:** In *Rotate Image*, process boundaries (top, right, bottom, left) to achieve $O(1)$ space.
- **Index Mapping:** For *Spiral Matrix*, maintain 4 boundaries (`top`, `bottom`, `left`, `right`) and shrink them after each side is processed.
- **1D Mapping:** A 2D matrix `M x N` can be mapped to a 1D array of size `M * N` using `index = r * N + c`.

---

## Quick Math
- **Subarrays in array of size N:** $N * (N + 1) / 2$
- **Pairs in array of size N:** $N * (N - 1) / 2$
- **Power of 2 check:** `(n > 0) && ((n & (n - 1)) == 0)`

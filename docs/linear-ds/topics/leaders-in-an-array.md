# Leaders in an Array

## Problem Summary
An element in an array is called a "Leader" if it is strictly greater than all the elements to its right side. The rightmost element is always a leader.

---

## Intuition
To be a leader, an element must "beat" every single element on its right. If we know the maximum value among all elements to the right of the current position, we only need to compare the current element with that maximum.

---

## Approach

### Brute Force
1. Use a nested loop.
2. The outer loop selects an element one by one from index `0` to `n-1`.
3. The inner loop checks all elements to the right of the selected element.
4. If no element to the right is greater than or equal to the selected element, it is a leader.
- **Time Complexity:** $O(N^2)$
- **Space Complexity:** $O(N)$ only to store and return the leaders.

### Optimal Approach (Scan from Right)
1. Initialize `max_from_right` with a very small value or the last element.
2. Iterate through the array from right to left.
3. For each element:
   - If the current element is strictly greater than `max_from_right`:
     - It is a leader. Store it in the results list.
     - Update `max_from_right` to the current element.
4. The leaders will be collected in reverse order (from right to left). If the problem requires leaders in their original relative order, reverse the result list.
- **Time Complexity:** $O(N)$ to traverse the array once.
- **Space Complexity:** $O(N)$ to store and return the leaders.

---

## Complexity
- **Time Complexity:** $O(N)$. Even with a reversal at the end, it remains linear.
- **Space Complexity:** $O(1)$ extra space if we don't count the space used to store the result. $O(N)$ if the result list is included.

---

## Pattern
- Two Pointers / Scanning from Right
- Keeping track of Running Maximum/Minimum

---

## Common Mistakes
- **Incorrect Comparison:** Using `>=` instead of `>` when the problem specifies "strictly greater".
- **Result Order:** Returning leaders in reverse order when the original order is expected.
- **Last Element:** Forgetting that the last element is always a leader.

---

## Related Problems
- Superior Elements
- Replace Elements with Greatest Element on Right Side
- Trapping Rain Water (uses similar suffix maximum logic)

# Largest Rectangle in Histogram

## Problem Summary
Given an array of integers `heights` representing the histogram's bar height where the width of each bar is 1, return the area of the largest rectangle in the histogram.

## Intuition
For every bar in the histogram, the largest rectangle that *includes* this bar as its height is limited by the first bars to its left and right that are shorter than it. 
- The boundary on the left is the **Previous Smaller Element (PSE)**.
- The boundary on the right is the **Next Smaller Element (NSE)**.
The width of the rectangle for a bar at index `i` is `(NSE_index - PSE_index - 1)`.

## Approach

### 1. Two-Pass Approach (Using PSE and NSE)
1. Precompute the `NSE` (Next Smaller Element) indices for all bars. If no smaller element exists to the right, use index `N`.
2. Precompute the `PSE` (Previous Smaller Element) indices for all bars. If no smaller element exists to the left, use index `-1`.
3. Iterate through each bar and calculate the area: `heights[i] * (NSE[i] - PSE[i] - 1)`.
4. Keep track of the maximum area.

### 2. One-Pass Optimal Approach (Monotonic Stack)
1. Use a monotonic stack to store indices of bars in increasing order of heights.
2. Iterate through the `heights` array:
    - If the current bar is shorter than the bar at the top of the stack, we have found the **NSE** for the stack's top bar. 
    - Pop the index from the stack (this is the `current_height`).
    - The **PSE** for this popped bar is the index now at the top of the stack (or `-1` if the stack is empty).
    - The **NSE** for this popped bar is the current index.
    - Calculate area: `heights[popped_index] * (current_index - new_top_index - 1)`.
3. After the loop, if bars remain in the stack, their **NSE** is the length of the array (`N`). Process them using the same logic.

## Complexity
- **Time Complexity**: $O(N)$ because every element is pushed and popped from the stack exactly once.
- **Space Complexity**: $O(N)$ for the stack.

## Pattern
Monotonic Stack (Next/Previous Smaller Element)

## Common Mistakes
- **Boundary Handling**: Forgetting to handle cases where no smaller element exists (using `-1` for left and `N` for right).
- **Width Calculation**: Off-by-one errors in `(right - left - 1)`.
- **Incomplete Stack**: Forgetting to process the remaining elements in the stack after the main loop finishes.

## Related Problems
- [Maximal Rectangle](./maximal-rectangle.md)
- [Trapping Rainwater](./trapping-rainwater.md)
- [Sum of Subarray Minimums](./sum-of-subarray-minimums.md)

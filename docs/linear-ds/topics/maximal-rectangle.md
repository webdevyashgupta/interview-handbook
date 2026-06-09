# Maximal Rectangle

## Problem Summary
Given a 2D binary matrix filled with 0's and 1's, find the largest rectangle containing only 1's and return its area.

## Intuition
This problem is an extension of the **Largest Rectangle in Histogram** problem. Each row in the matrix can be treated as the base of a histogram. The height of each bar in the histogram is determined by the number of consecutive 1's stretching upwards from that row. By iterating through each row and updating these heights, we can solve the problem row by row using the histogram logic.

## Approach
1. Initialize a `heights` array of size `M` (number of columns) with all zeros.
2. Iterate through each row of the matrix:
    - For each column in the current row:
        - If the matrix element is '1', increment the corresponding value in the `heights` array (`heights[j]++`).
        - If the matrix element is '0', reset the height for that column to zero (`heights[j] = 0`).
    - After updating the `heights` for the current row, calculate the largest rectangle area in that "row-based histogram" using the **Largest Rectangle in Histogram** logic (monotonic stack).
3. Maintain a global `max_area` and update it after processing each row.
4. Return the `max_area`.

## Implementation

```python
def maximalRectangle(matrix: list[list[str]]) -> int:
    if not matrix or not matrix[0]:
        return 0
    
    cols = len(matrix[0])
    heights = [0] * (cols + 1)
    max_area = 0
    
    for row in matrix:
        for i in range(cols):
            heights[i] = heights[i] + 1 if row[i] == '1' else 0
            
        # Largest Rectangle in Histogram logic
        stack = [-1]
        for i in range(cols + 1):
            while heights[i] < heights[stack[-1]]:
                h = heights[stack.pop()]
                w = i - stack[-1] - 1
                max_area = max(max_area, h * w)
            stack.append(i)
            
    return max_area
```

## Complexity
- **Time Complexity**: $O(N \times M)$, where $N$ is the number of rows and $M$ is the number of columns. We process each cell once and run a histogram calculation ($O(M)$) for each row.
- **Space Complexity**: $O(M)$ to store the `heights` array and the stack used in the histogram calculation.

## Pattern
Reduction to Histogram, Monotonic Stack.

## Common Mistakes
- **Not Resetting Heights**: Forgetting to set `heights[j] = 0` when `matrix[i][j] == '0'`. This is crucial because a '0' breaks the continuity of the rectangle's height.
- **Inefficient Re-computation**: Recalculating heights from scratch for each row instead of updating the previous row's heights.

## Related Problems
- [Largest Rectangle in Histogram](./largest-rectangle-in-histogram.md)
- [Maximal Square](../../26_math_dp_extra.md)
- [Count Square Submatrices with All Ones](../../striver-dp/topics/count-square-submatrices-with-all-ones.md)

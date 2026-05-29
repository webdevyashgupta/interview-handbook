# Spiral Traversal of a Matrix

## Problem Summary
Given an `N x M` matrix, return all elements of the matrix in spiral order, starting from the top-left and moving inwards.

## Intuition
The spiral traversal follows a consistent pattern: move right across the top row, then down the rightmost column, then left across the bottom row, and finally up the leftmost column. This process repeats with a smaller inner matrix until all elements are visited.

## Approach

### Optimal (Layer-by-Layer Simulation)
Since we must visit every element, the optimal approach is an $O(N \times M)$ simulation using four boundaries.

1.  Initialize four pointers:
    - `top`: Row index of the upper boundary (initially 0).
    - `bottom`: Row index of the lower boundary (initially $N-1$).
    - `left`: Column index of the left boundary (initially 0).
    - `right`: Column index of the right boundary (initially $M-1$).
2.  While `top <= bottom` and `left <= right`:
    - **Move Right:** Traverse from `left` to `right` on the `top` row. Increment `top`.
    - **Move Down:** Traverse from `top` to `bottom` on the `right` column. Decrement `right`.
    - **Move Left:** (Check if `top <= bottom`) Traverse from `right` to `left` on the `bottom` row. Decrement `bottom`.
    - **Move Up:** (Check if `left <= right`) Traverse from `bottom` to `top` on the `left` column. Increment `left`.

## Complexity
- **Time Complexity:** $O(N \times M)$, where $N$ is the number of rows and $M$ is the number of columns, as each element is visited exactly once.
- **Space Complexity:** $O(1)$ if we don't count the output list, or $O(N \times M)$ to store the result.

## Pattern
Matrix Traversal (Boundary Manipulation).

## Common Mistakes
- **Boundary Overshoot:** Forgetting to check `top <= bottom` or `left <= right` before the "Left" and "Up" movements can lead to duplicate prints in non-square matrices or matrices with a single row/column.
- **Incorrect Updates:** Incrementing/decrementing the wrong pointer after a traversal direction.

## Related Problems
- Spiral Matrix II (Generate the matrix)
- Rotate Image
- Set Matrix Zeroes

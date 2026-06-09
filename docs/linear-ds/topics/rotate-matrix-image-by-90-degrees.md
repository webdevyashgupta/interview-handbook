# Rotate Matrix by 90 Degrees

## Problem Summary
You are given an `n x n` 2D matrix representing an image. Rotate the image by 90 degrees (clockwise) in-place. You should not allocate another 2D matrix for the rotation.

---

## Intuition
Rotating a matrix clockwise by 90 degrees is equivalent to two simpler operations:
1. Flipping the matrix over its main diagonal (Transposing).
2. Flipping the matrix horizontally (Reversing each row).

---

## Approach

### Brute Force
1. Create a new dummy matrix of the same size.
2. Observe the mapping: The element at `matrix[i][j]` in the original matrix moves to the `j`-th row and `(n - 1 - i)`-th column of the new matrix.
   - `ans[j][n - 1 - i] = matrix[i][j]`
3. Copy the elements from the dummy matrix back to the original.
- **Time Complexity:** $O(N^2)$
- **Space Complexity:** $O(N^2)$ for the extra matrix.

### Optimal Approach (In-Place)
To achieve $O(1)$ extra space, perform the transformation in two steps:

**Step 1: Transpose the Matrix**
- Swap `matrix[i][j]` with `matrix[j][i]` for all `i` and `j` where `i < j`.
- This converts rows into columns.
```cpp
for (int i = 0; i < n; i++) {
    for (int j = i + 1; j < n; j++) {
        swap(matrix[i][j], matrix[j][i]);
    }
}
```

**Step 2: Reverse Each Row**
- Iterate through each row and reverse its elements.
```cpp
for (int i = 0; i < n; i++) {
    reverse(matrix[i].begin(), matrix[i].end());
}
```
- **Time Complexity:** $O(N^2)$
- **Space Complexity:** $O(1)$

## Implementation

```python
def rotate(matrix: list[list[int]]) -> None:
    n = len(matrix)
    
    # Step 1: Transpose the matrix
    for i in range(n):
        for j in range(i + 1, n):
            matrix[i][j], matrix[j][i] = matrix[j][i], matrix[i][j]
            
    # Step 2: Reverse each row
    for i in range(n):
        matrix[i].reverse()
```

---

## Complexity
- **Time Complexity:** $O(N^2)$ — $O(N^2/2)$ for transposition and $O(N^2/2)$ for row reversal.
- **Space Complexity:** $O(1)$ as the operations are performed in-place.

---

## Pattern
- **Matrix Manipulation:** Using common transformations (Transpose + Reverse) to achieve rotation.
- **In-place algorithms.**

---

## Common Mistakes
- **Full Transpose:** Swapping `(i, j)` and `(j, i)` for the entire matrix instead of just the upper/lower triangle. Doing it for the whole matrix swaps elements twice, resulting in the original matrix.
- **Counter-Clockwise:** Reversing the columns instead of rows would rotate the matrix counter-clockwise (or transposing the other diagonal).
- **Not Square:** Assuming the matrix is rectangular. This specific trick (Transpose + Reverse) only works for square matrices.

---

## Related Problems
- Rotate Image (Counter-Clockwise)
- Spiral Matrix
- Set Matrix Zeroes

# Set Matrix Zeroes

## Problem Summary
Given an `m x n` integer matrix, if an element is `0`, set its entire row and column to `0`. The task must be performed in-place.

---

## Intuition
The challenge is that setting a row or column to zero immediately might cause you to treat those newly set zeros as "original" zeros, leading to the entire matrix becoming zero. We need a way to mark rows and columns for zeroing without affecting the current traversal.

---

## Approach

### Brute Force
1. Traverse the matrix.
2. For every `matrix[i][j] == 0`, iterate through its entire row and column.
3. Mark every non-zero element in that row and column with a special value (e.g., `-1`), provided `-1` is not a valid matrix value.
4. After the full traversal, iterate through the matrix again and change all `-1`s to `0`.
- **Time Complexity:** $O((N \times M) \times (N + M))$
- **Space Complexity:** $O(1)$ (if using a special value like `-1`).

### Better Approach (Using Auxiliary Arrays)
1. Use two separate arrays: `row[N]` and `col[M]`, both initialized to 0.
2. Traverse the matrix. If `matrix[i][j] == 0`, set `row[i] = 1` and `col[j] = 1`.
3. Re-traverse the matrix. For each element at `(i, j)`, if `row[i] == 1` or `col[j] == 1`, set `matrix[i][j] = 0`.
- **Time Complexity:** $O(2 \times (N \times M))$
- **Space Complexity:** $O(N + M)$

### Optimal Approach (In-Place using First Row/Column)
Instead of using external arrays, use the first row and first column of the matrix itself to store the marker information.
1. Use an extra variable `col0` to track the state of the first column because `matrix[0][0]` is shared between the first row and first column.
2. **Step 1:** Iterate through the matrix. If `matrix[i][j] == 0`:
   - Mark the corresponding row: `matrix[i][0] = 0`.
   - Mark the corresponding column: If $j \neq 0$, `matrix[0][j] = 0`. Else, set `col0 = 0`.
3. **Step 2:** Iterate from `(1, 1)` to `(N-1, M-1)`. If `matrix[i][0] == 0` or `matrix[0][j] == 0`, set `matrix[i][j] = 0`.
4. **Step 3:** Handle the first row: If `matrix[0][0] == 0`, set all elements in the first row to 0.
5. **Step 4:** Handle the first column: If `col0 == 0`, set all elements in the first column to 0.
- **Time Complexity:** $O(2 \times (N \times M))$
- **Space Complexity:** $O(1)$

## Implementation

```python
def setZeroes(matrix: list[list[int]]) -> None:
    n = len(matrix)
    m = len(matrix[0])
    col0 = 1
    
    # Step 1: Mark rows and columns
    for i in range(n):
        for j in range(m):
            if matrix[i][j] == 0:
                matrix[i][0] = 0
                if j != 0:
                    matrix[0][j] = 0
                else:
                    col0 = 0
                    
    # Step 2: Fill zeros for (1, 1) to (n-1, m-1)
    for i in range(1, n):
        for j in range(1, m):
            if matrix[i][0] == 0 or matrix[0][j] == 0:
                matrix[i][j] = 0
                
    # Step 3: Handle first row
    if matrix[0][0] == 0:
        for j in range(m):
            matrix[0][j] = 0
            
    # Step 4: Handle first column
    if col0 == 0:
        for i in range(n):
            matrix[i][0] = 0
```

---

## Complexity
- **Time Complexity:** $O(N \times M)$
- **Space Complexity:** $O(1)$

---

## Pattern
- In-place modification.
- Using the data structure itself (first row/column) as a hash/storage.

---

## Common Mistakes
- **Order of Updates:** Updating the first row/column markers before the rest of the matrix can lead to a "cascade" of zeros.
- **Shared Marker:** Forgetting that `matrix[0][0]` represents both row 0 and column 0, necessitating an extra variable like `col0`.

---

## Related Problems
- Game of Life
- Spiral Matrix
- Rotate Image

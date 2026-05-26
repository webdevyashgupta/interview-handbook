# Count Square Submatrices with All Ones

## 🧩 Problem Summary
Given an `n x m` matrix filled with `0`s and `1`s, return the total number of square submatrices that have all ones. A square submatrix is defined as a submatrix where the number of rows equals the number of columns.

**Input:** A 2D grid `matrix` of size `n x m`.
**Output:** An integer representing the total count of square submatrices.

### Constraints
- $n, m \geq 1$
- Matrix contains only $0$s and $1$s.

---

## 💡 Intuition
The core idea is to count how many squares **end** at each cell $(i, j)$, where $(i, j)$ is the bottom-right corner of the square.

1.  **Why Brute Force Fails:** A brute force approach would involve picking every possible top-left corner, every possible size, and checking if all elements in that square are 1s. This is computationally expensive ($O(N^3)$ or $O(N^4)$).
2.  **Thought Process:** 
    - If a cell $(i, j)$ is to be the bottom-right corner of a square of size $k$, then the cells to its top $(i-1, j)$, left $(i, j-1)$, and top-left diagonal $(i-1, j-1)$ must all be bottom-right corners of squares of at least size $k-1$.
    - The number of squares ending at $(i, j)$ is exactly equal to the side length of the largest square ending at $(i, j)$. For example, if the largest square ending at $(i, j)$ has a side of 3, then there are also squares of side 1 and 2 ending there.

---

## 🔁 Approach
### 1. Tabulation (Bottom-Up DP)
We use a DP table where `dp[i][j]` stores the number of square submatrices ending at $(i, j)$.

1.  Initialize a `dp` table of the same size as the input `matrix`.
2.  **Base Case:** The first row and the first column of the `dp` table are the same as the input `matrix` because the largest square ending at those cells can only be of size 1 (if the cell is 1).
3.  **Filling the Table:** For each cell $(i, j)$ starting from $(1, 1)$:
    - If `matrix[i][j] == 1`:
        `dp[i][j] = 1 + min(dp[i-1][j], dp[i-1][j-1], dp[i][j-1])`
    - If `matrix[i][j] == 0`:
        `dp[i][j] = 0`
4.  **Final Result:** The total number of squares is the sum of all entries in the `dp` table.

```python
def countSquares(matrix):
    n = len(matrix)
    m = len(matrix[0])
    
    # Initialize DP table
    dp = [[0] * m for _ in range(n)]
    
    # Fill base cases (first row and first column)
    for i in range(n):
        dp[i][0] = matrix[i][0]
    for j in range(m):
        dp[0][j] = matrix[0][j]
        
    # Fill the rest of the DP table
    for i in range(1, n):
        for j in range(1, m):
            if matrix[i][j] == 1:
                dp[i][j] = 1 + min(dp[i-1][j], dp[i-1][j-1], dp[i][j-1])
            else:
                dp[i][j] = 0
                
    # Sum all values in dp table
    total_squares = sum(sum(row) for row in dp)
    return total_squares
```

---

## 🧠 DP State Definition
`dp[i][j]` represents the side length of the largest square submatrix whose bottom-right corner is at cell `(i, j)`. This value also corresponds to the total number of square submatrices ending at `(i, j)`.

---

## 🔄 Recurrence Relation
For `matrix[i][j] == 1`:
$$dp[i][j] = 1 + \min(dp[i-1][j], dp[i-1][j-1], dp[i][j-1])$$

For `matrix[i][j] == 0`:
$$dp[i][j] = 0$$

---

## ⏱️ Complexity
- **Time Complexity:** $O(N \times M)$ where $N$ and $M$ are the dimensions of the matrix. We traverse the matrix once.
- **Space Complexity:** $O(N \times M)$ for the DP table. (Note: This can be optimized to $O(M)$ by using only the previous row).

---

## 📌 Pattern
**DP on Rectangles / Squares.** This pattern involves using neighboring cells (top, left, diagonal) to determine the property of the current cell in a 2D grid.

---

## ⚠️ Common Mistakes
- **Incorrect Base Case:** Forgetting that the first row and column cannot form squares larger than size 1.
- **Off-by-one errors:** Starting the loops from index 0 instead of 1 when using the recurrence.
- **Ignoring 0s:** Not explicitly setting `dp[i][j] = 0` when `matrix[i][j]` is 0 (though initialization handles this, it's a logical step).
- **Summing logic:** Trying to find the max value in the DP table (which is for the "Maximal Square" problem) instead of summing all values.

---

## 🔗 Related Problems
1.  **Maximal Square:** Find the area of the largest square submatrix containing only 1s.
2.  **Maximal Rectangle:** Find the largest rectangle containing only 1s (uses a different histogram-based DP).
3.  **Largest Plus Sign:** Find the order of the largest axis-aligned plus sign of 1s.

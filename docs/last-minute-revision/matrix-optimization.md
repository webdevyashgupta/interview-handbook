# Matrix Optimization

This section covers advanced techniques for matrix manipulation, including optimized search and median calculations.

---

## 1. Search in a 2D Matrix II

**Difficulty: 🟡 Medium** | **Pattern: Top-Right / Bottom-Left Search**

### Problem Summary
Write an efficient algorithm that searches for a value `target` in an `m x n` integer matrix. The matrix has the following properties:
- Integers in each row are sorted in ascending from left to right.
- Integers in each column are sorted in ascending from top to bottom.

### Intuition
Standard Binary Search doesn't work directly because the 1D layout is not fully sorted. However, if we start from the **top-right corner**:
- If `current > target`: The entire column must be greater than target (since columns are sorted). Move **left**.
- If `current < target`: The entire row must be smaller than target (since rows are sorted). Move **down**.

### Implementation

```python
def searchMatrix(matrix: list[list[int]], target: int) -> bool:
    if not matrix or not matrix[0]: return False
    
    rows, cols = len(matrix), len(matrix[0])
    r, c = 0, cols - 1 # Start top-right
    
    while r < rows and c >= 0:
        if matrix[r][c] == target:
            return True
        elif matrix[r][c] > target:
            c -= 1 # Move left
        else:
            r += 1 # Move down
            
    return False
```

**Time:** $O(M + N)$ | **Space:** $O(1)$

---

## 2. Matrix Median

**Difficulty: 🔴 Hard** | **Pattern: BS on Answer**

### Problem Summary
Given a matrix of integers of size `R x C` where each row is sorted. Find the median of the matrix. (Assuming `R*C` is odd).

### Intuition
The median is the number which has at least `(R*C + 1) // 2` numbers smaller than or equal to it.
We can Binary Search on the **value range** of the matrix: `[min_element, max_element]`. 
For each `mid`, count how many numbers in the matrix are $\le$ `mid`.

### Implementation

```python
import bisect

def findMedian(matrix):
    r, c = len(matrix), len(matrix[0])
    
    low = min(row[0] for row in matrix)
    high = max(row[-1] for row in matrix)
    
    required = (r * c + 1) // 2
    
    while low <= high:
        mid = (low + high) // 2
        
        # Count elements <= mid
        count = 0
        for row in matrix:
            count += bisect.bisect_right(row, mid)
            
        if count < required:
            low = mid + 1
        else:
            high = mid - 1
            
    return low
```

**Time:** $O(32 \times R \log C)$ | **Space:** $O(1)$

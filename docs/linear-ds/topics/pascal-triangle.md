# Pascal's Triangle

## Problem Summary
Pascal's Triangle is a triangular array of binomial coefficients. There are three common variations:
1.  **Variation 1:** Find the element at row $R$ and column $C$.
2.  **Variation 2:** Print the $N^{th}$ row of Pascal's Triangle.
3.  **Variation 3:** Given $N$, generate all $N$ rows.

## Intuition
The core property of Pascal's Triangle is that each element is the sum of the two elements directly above it. Mathematically, the element at row $r$ and column $c$ (using 0-indexing) is given by the combination formula: $\binom{r}{c} = \frac{r!}{c! \cdot (r-c)!}$.

## Approach

### Variation 1: Element at Row R, Col C
Use the formula $(r-1)C(c-1)$. To calculate $nCr$ efficiently:
$nCr = \frac{n \times (n-1) \times \dots \times (n-r+1)}{r \times (r-1) \times \dots \times 1}$.
- **Complexity:** $O(C)$ time, $O(1)$ space.

### Variation 2: Print Row N
Instead of calculating $nCr$ for every element from scratch (which is $O(N \times R)$), use the previous element to find the current one:
- The $1^{st}$ element is 1.
- The $k^{th}$ element is $prev \times \frac{n-k}{k}$.
- **Complexity:** $O(N)$ time, $O(1)$ space (excluding output).

### Variation 3: Generate Entire Triangle
Iterate through rows 1 to $N$. For each row, use the optimized approach from Variation 2 to generate elements.
- **Complexity:** $O(N^2)$ time, $O(N^2)$ space to store the triangle.

## Implementation

```python
def generate(numRows: int) -> list[list[int]]:
    triangle = []
    
    for row_num in range(numRows):
        # The first and last row elements are always 1.
        row = [None for _ in range(row_num + 1)]
        row[0], row[-1] = 1, 1
        
        # Each triangle element is equal to the sum of the elements
        # above-and-to-the-left and above-and-to-the-right.
        for j in range(1, len(row) - 1):
            row[j] = triangle[row_num - 1][j - 1] + triangle[row_num - 1][j]
        
        triangle.append(row)
        
    return triangle
```

## Complexity
- **Time Complexity:** $O(N^2)$ for generating $N$ rows.
- **Space Complexity:** $O(N^2)$ to store the result.

## Pattern
Combinatorics and Mathematical Observation.

## Common Mistakes
- **1-based vs 0-based indexing:** Pascal's triangle problems often use 1-based indexing for rows and columns; ensure you adjust to 0-based for calculations.
- **Integer Overflow:** $nCr$ values grow very fast. Use `long` in languages like Java/C++.

## Related Problems
- Binomial Coefficients
- Combination Sum
- Unique Paths

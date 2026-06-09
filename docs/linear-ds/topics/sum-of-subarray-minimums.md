# Sum of Subarray Minimums

### Problem Summary
Given an array of integers `arr`, find the sum of `min(b)`, where `b` ranges over every (contiguous) subarray of `arr`. Since the answer may be large, return the answer modulo $10^9 + 7$.

### Intuition
A brute force approach would take $O(N^2)$ by generating all subarrays. To optimize, we shift our focus from "subarrays" to "elements." We ask: **How many subarrays have `arr[i]` as their minimum element?**
If `arr[i]` is the minimum for $X$ subarrays, its total contribution to the sum is `arr[i] * X`.

### Approach
#### Contribution-Based Approach (Optimal)
1. **Define Boundaries**: For each element `arr[i]`, find:
   - `left[i]`: The number of elements to its left that are strictly greater than `arr[i]` until a smaller element is encountered.
   - `right[i]`: The number of elements to its right that are greater than or equal to `arr[i]` until a smaller element is encountered.
2. **Handle Duplicates**: To avoid double-counting subarrays when identical elements exist (e.g., `[1, 1]`), we use a "strict" comparison on one side (e.g., Previous Smaller Element) and a "non-strict" comparison on the other (e.g., Next Smaller or Equal Element).
3. **Calculate Contribution**: The number of subarrays where `arr[i]` is the minimum is `(left_dist + 1) * (right_dist + 1)`.
   - `left_dist` is the distance to the Previous Smaller Element.
   - `right_dist` is the distance to the Next Smaller Element.
4. **Monotonic Stack**: Use a monotonic stack twice (or once with smart logic) to precompute the indices of the Previous Smaller Element (PSE) and Next Smaller Element (NSE).
5. **Final Sum**: `total = sum(arr[i] * (i - PSE[i]) * (NSE[i] - i)) % mod`.

## Implementation

```python
def sumSubarrayMins(arr: list[int]) -> int:
    MOD = 10**9 + 7
    n = len(arr)
    # Distance to Previous Smaller Element
    left = [0] * n
    # Distance to Next Smaller or Equal Element
    right = [0] * n
    
    stack = []
    for i in range(n):
        while stack and arr[stack[-1]] > arr[i]:
            stack.pop()
        left[i] = i - stack[-1] if stack else i + 1
        stack.append(i)
        
    stack = []
    for i in range(n - 1, -1, -1):
        while stack and arr[stack[-1]] >= arr[i]:
            stack.pop()
        right[i] = stack[-1] - i if stack else n - i
        stack.append(i)
        
    ans = sum(a * l * r for a, l, r in zip(arr, left, right))
    return ans % MOD
```

### Complexity
- **Time Complexity**: $O(N)$ - Precomputing PSE and NSE takes $O(N)$, and the final sum takes $O(N)$.
- **Space Complexity**: $O(N)$ - To store the PSE, NSE, and the stack.

### Pattern
- **Contribution Technique**: Solving "how much does this element contribute to the final answer?"
- **Monotonic Stack**: Finding the nearest smaller element on both sides.

### Common Mistakes
- **Double Counting**: Not handling duplicate elements correctly by using strict comparison on one side and non-strict on the other.
- **Integer Overflow**: Forgetting to use `long` (in Java/C++) or applying modulo at each multiplication step.
- **Index Out of Bounds**: Not correctly handles cases where no smaller element exists on the left (use -1) or right (use N).

### Related Problems
- Sum of Subarray Maximums
- Sum of Subarray Ranges
- Largest Rectangle in Histogram
- Online Stock Span

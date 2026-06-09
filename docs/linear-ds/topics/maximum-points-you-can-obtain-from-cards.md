# Maximum Points You Can Obtain from Cards

## Problem Summary
There are several cards arranged in a row, and each card has an associated number of points. You can take exactly $K$ cards from either the beginning or the end of the row. Your goal is to maximize the total points you obtain.

## Intuition
Since we can only pick cards from the ends, we are essentially picking some number of cards $x$ from the left and $K-x$ cards from the right. A sliding window approach can help us transition between these different combinations efficiently.

## Approach

### Optimal (Sliding Window / Two Pointers)
- **Method**: 
    1. Start by picking all $K$ cards from the left end. Calculate this initial `sum`.
    2. Set `maxSum = currentSum`.
    3. Now, iteratively "remove" one card from the left end (starting from the rightmost card of our current left-selection) and "add" one card from the right end of the array.
    4. For each step, update the `currentSum` and compare it with `maxSum`.
- **Steps**:
    - `leftSum = sum(nums[0...K-1])`
    - `rightSum = 0`
    - `maxSum = leftSum`
    - For $i$ from $K-1$ down to $0$:
        - `leftSum -= nums[i]`
        - `rightSum += nums[lastIndex]`
        - `lastIndex--`
        - `maxSum = max(maxSum, leftSum + rightSum)`
- **Alternative Interpretation**: The problem is equivalent to finding a contiguous subarray of size $(N-K)$ with the minimum sum. The cards we *don't* pick must be contiguous in the middle.

## Implementation

```python
def maxScore(cardPoints: list[int], k: int) -> int:
    n = len(cardPoints)
    left_sum = sum(cardPoints[:k])
    max_sum = left_sum
    right_sum = 0
    
    for i in range(k - 1, -1, -1):
        left_sum -= cardPoints[i]
        right_sum += cardPoints[n - (k - i)]
        max_sum = max(max_sum, left_sum + right_sum)
        
    return max_sum
```

## Complexity
- **Time**: $O(K)$ - We first sum $K$ elements, then perform $K$ updates.
- **Space**: $O(1)$ - Only a few variables to store sums and pointers.

## Pattern
- **Sliding Window (Constant Size)**: Though it's "wrapped" around the ends, it's essentially moving a window of elements we *don't* pick or shifting between two selection pools.

## Common Mistakes
- **Index Management**: Be careful when moving the pointer from the end of the array (`n-1`, `n-2`, etc.).
- **Initial Sum**: Forgetting to include the case where all $K$ cards are taken from the left (or all from the right).

## Related Problems
- Subarray Sum Equals K
- Maximum Subarray
- Rotating the Array

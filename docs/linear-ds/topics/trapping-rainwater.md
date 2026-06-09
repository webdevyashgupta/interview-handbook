# Trapping Rainwater

### Problem Summary
Given an array of non-negative integers representing the height of buildings (where each building has a width of 1), calculate how much water it can trap after raining.

### Intuition
Water is trapped on top of a building if there are taller buildings both to its left and to its right. The amount of water trapped on top of a building at index `i` is determined by the minimum of the maximum height to its left and the maximum height to its right, minus the height of the building itself:
`Water[i] = min(leftMax, rightMax) - height[i]`

### Approach

#### 1. Prefix and Suffix Max Arrays (Better)
1. **Left Max Array**: Precompute an array `prefixMax` where `prefixMax[i]` stores the maximum height from the start up to index `i`.
2. **Right Max Array**: Precompute an array `suffixMax` where `suffixMax[i]` stores the maximum height from index `i` to the end.
3. **Calculation**: Total water is $\sum_{i=0}^{n-1} (\min(prefixMax[i], suffixMax[i]) - height[i])$.
- **Time Complexity**: $O(N)$
- **Space Complexity**: $O(N)$

#### 2. Two-Pointer Approach (Optimal)
To optimize space to $O(1)$, use two pointers `left` and `right`.
1. **Initialize**: `left = 0`, `right = n-1`, `leftMax = 0`, `rightMax = 0`, `total = 0`.
2. **Loop**: While `left < right`:
    - If `height[left] <= height[right]`:
        - We know there's a building at least as tall as `height[left]` on the right.
        - If `height[left] >= leftMax`, update `leftMax`.
        - Else, `total += leftMax - height[left]`.
        - `left++`.
    - Else:
        - We know there's a building at least as tall as `height[right]` on the left.
        - If `height[right] >= rightMax`, update `rightMax`.
        - Else, `total += rightMax - height[right]`.
        - `right--`.
- **Time Complexity**: $O(N)$
- **Space Complexity**: $O(1)$

## Implementation

```python
def trap(height: list[int]) -> int:
    if not height:
        return 0
    
    l, r = 0, len(height) - 1
    left_max, right_max = 0, 0
    res = 0
    
    while l < r:
        if height[l] < height[r]:
            if height[l] >= left_max:
                left_max = height[l]
            else:
                res += left_max - height[l]
            l += 1
        else:
            if height[r] >= right_max:
                right_max = height[r]
            else:
                res += right_max - height[r]
            r -= 1
            
    return res
```

### Complexity
- **Time Complexity**: $O(N)$ for both optimized approaches.
- **Space Complexity**: $O(N)$ with auxiliary arrays, $O(1)$ with the two-pointer approach.

### Pattern
- **Two Pointers**: Moving from both ends toward the middle based on comparative heights.
- **Precomputation**: Using prefix/suffix arrays to avoid redundant calculations.

### Common Mistakes
- **Negative Water**: Forgetting to ensure that $\min(leftMax, rightMax) - height[i]$ is not negative (though with correct logic it won't be).
- **Pointer Movement**: Moving the wrong pointer in the two-pointer approach. Always move the pointer pointing to the smaller height to ensure the "shorter" side of the container is correctly identified.

### Related Problems
- Container With Most Water
- Product of Array Except Self
- Next Greater Element

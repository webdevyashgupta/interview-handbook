# Next Greater Element II

### Problem Summary
Given a circular integer array `nums`, return the next greater element for every element in `nums`. The next greater element of a number `x` is the first greater number to its traversing-order next in the array, which means you could search circularly to find its next greater number. If it doesn't exist, return -1 for this number.

### Intuition
The core challenge is the circular nature of the array. A brute force approach would involve checking elements to the right and then wrapping around to the beginning. To optimize, we can virtually double the array to simulate circularity. Using a monotonic stack helps us find the next greater element in linear time by keeping track of elements for which we haven't found a greater element yet.

### Approach
#### Monotonic Stack (Optimal)
1. **Virtual Doubling**: Instead of actually doubling the array (which takes $O(N)$ space), we iterate from $2N-1$ down to $0$ and use the index `i % N` to access elements.
2. **Stack Maintenance**: Use a stack to store elements in a way that the top of the stack is always the next potential greater element.
3. **Iteration**:
    - Iterate from $2N-1$ to $0$.
    - While the stack is not empty and the top element is less than or equal to the current element `nums[i % N]`, pop from the stack.
    - If `i < N`, the top of the stack (if it exists) is the next greater element for `nums[i]`. If the stack is empty, it's -1.
    - Push the current element `nums[i % N]` onto the stack.

## Implementation

```python
def nextGreaterElements(nums: list[int]) -> list[int]:
    n = len(nums)
    res = [-1] * n
    stack = [] # Decreasing stack
    
    # Iterate through the array twice to simulate circularity
    for i in range(2 * n - 1, -1, -1):
        while stack and stack[-1] <= nums[i % n]:
            stack.pop()
            
        if i < n:
            if stack:
                res[i] = stack[-1]
                
        stack.append(nums[i % n])
        
    return res
```

### Complexity
- **Time Complexity**: $O(N)$ - We iterate through $2N$ elements, and each element is pushed and popped at most once.
- **Space Complexity**: $O(N)$ - For the stack and the output array.

### Pattern
- **Monotonic Stack**: Specifically a decreasing stack (from top to bottom) to find the next greater element.
- **Circular Array**: Using `i % N` to simulate a doubled array.

### Common Mistakes
- **Actually Doubling the Array**: This increases space complexity unnecessarily.
- **Incorrect Loop Range**: Forgetting to iterate up to $2N$ to ensure every element has a chance to see all other elements in a circular fashion.
- **Stack Comparison**: Using `<` instead of `<=` when popping, which might lead to incorrect results if duplicate elements exist.

### Related Problems
- Next Greater Element I
- Next Smaller Element
- Daily Temperatures
- Online Stock Span

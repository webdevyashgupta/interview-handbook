# Min Stack Implementation

### Problem Summary
Design a stack that supports `push`, `pop`, `top`, and retrieving the minimum element in **O(1)** time.

### Intuition
A standard stack doesn't keep track of the minimum element efficiently. To get the minimum in O(1), we need to store additional information along with each element that represents the minimum value in the stack *at that point in time*.

### Approach

#### 1. Using Pairs (Space: O(2N))
- Each element in the stack is a pair: `(value, current_min)`.
- When pushing `x`:
  - If the stack is empty, push `(x, x)`.
  - If not empty, push `(x, min(x, stack.top().min_val))`.
- `getMin()` simply returns `stack.top().min_val`.

#### 2. Using an Optimized Space Approach (Space: O(N))
- Maintain a single variable `minVal`.
- **Push**:
  - If `x < minVal`: Push `2*x - minVal` and update `minVal = x`.
  - Else: Push `x`.
- **Pop**:
  - If `top < minVal`: The actual value being popped is `minVal`. Update `minVal = 2*minVal - top`.
  - Else: Pop `top`.
- This approach uses mathematical encoding to store the previous minimum within the stack itself when a new minimum is found.

### Complexity
- **Time Complexity**: **O(1)** for all operations (`push`, `pop`, `top`, `getMin`).
- **Space Complexity**: **O(N)** to store the elements.

### Pattern
State-tracking in Stack.

### Common Mistakes
- **Overflow**: When using the optimized mathematical approach, `2*x - minVal` might exceed the range of standard integers (use `long long` in C++ or similar).
- **Popping the Minimum**: Forgetting to update the `minVal` when the current minimum element is popped from the stack.

### Related Problems
- Max Stack
- Implement Queue using Stacks
- Sliding Window Maximum

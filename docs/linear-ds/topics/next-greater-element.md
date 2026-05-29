# Next Greater Element

### Problem Summary
Given an array, for each element, find the first element to its right that is greater than it. If no such element exists, return -1.

### Intuition
A brute-force approach (O(N²)) involves checking all elements to the right for every element. To optimize, we can use a **Monotonic Stack**. The stack will keep track of potential "next greater" candidates in a way that we don't have to re-scan the array multiple times.

### Approach
The optimal approach involves traversing the array from **right to left**:
1. Initialize an empty stack and a result array of the same size as the input.
2. Iterate through the array from `n-1` down to `0`:
   - While the stack is not empty and `stack.top() <= current_element`:
     - Pop from the stack (these elements are smaller and won't be the "next greater" for anything to the left of the current element).
   - If the stack is empty:
     - The next greater element is -1.
   - Else:
     - The next greater element is `stack.top()`.
   - Push the `current_element` onto the stack.
3. Return the result array.

### Complexity
- **Time Complexity**: **O(N)**. Although there's a nested while loop, each element is pushed and popped from the stack at most once.
- **Space Complexity**: **O(N)** for the stack in the worst case (array sorted in decreasing order).

### Pattern
**Monotonic Stack** (maintaining elements in a specific order).

### Common Mistakes
- **Traversal Direction**: Forgetting that traversing from right-to-left is usually more intuitive for "Next Greater to the right".
- **Strictly Greater**: Not handling the equality case correctly (`stack.top() <= current_element` vs `<`).
- **Circular Array**: If the problem asks for the next greater element in a circular array, traverse the array twice (using `i % n`).

### Related Problems
- Next Greater Element II (Circular)
- Next Smaller Element
- Daily Temperatures
- Largest Rectangle in Histogram
- Trapping Rain Water

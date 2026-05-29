# Previous Smaller Element (Nearest Smaller Element)

### Problem Summary
Given an array of integers, for every element, find the nearest element to its left that is smaller than it. If no such element exists, return -1.

### Intuition
For any element `arr[i]`, we need to look backwards (to the left) to find the first element `arr[j]` such that `arr[j] < arr[i]` and `j < i`. A brute force $O(N^2)$ would check every element on the left. To optimize to $O(N)$, we can use a monotonic stack to maintain elements that could potentially be the nearest smaller element for future elements.

### Approach
#### Monotonic Stack (Optimal)
1. **Initialize**: An empty stack and an output array.
2. **Traverse**: Iterate through the array from left to right (index $0$ to $N-1$).
3. **Stack Maintenance**:
    - While the stack is not empty and the top of the stack is greater than or equal to the current element `arr[i]`, pop from the stack.
    - This is because a larger element to the left of `arr[i]` can never be the "nearest smaller" for any element to the right of `arr[i]`. `arr[i]` is smaller and closer, so it's a better candidate.
4. **Result Assignment**:
    - If the stack is empty, there is no smaller element to the left. Assign -1.
    - If the stack is not empty, the top of the stack is the nearest smaller element.
5. **Push**: Push the current element `arr[i]` onto the stack.

### Complexity
- **Time Complexity**: $O(N)$ - Each element is pushed and popped at most once.
- **Space Complexity**: $O(N)$ - For the stack and the output array.

### Pattern
- **Monotonic Stack**: Specifically an increasing stack (from bottom to top) to find the nearest smaller element on the left.

### Common Mistakes
- **Traversal Direction**: Forgetting that for *previous* elements, we must traverse from left to right to build the context of what came before.
- **Pop Condition**: Using `>` instead of `>=`. Usually, if the elements are equal, the "nearest" smaller must be strictly smaller.
- **Modifying Input**: It's better to return a new array rather than modifying the original input array.

### Related Problems
- Next Greater Element
- Next Smaller Element
- Largest Rectangle in Histogram
- Trapping Rainwater

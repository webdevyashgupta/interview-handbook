# Balanced Parentheses

### Problem Summary
Given a string containing characters like `(`, `)`, `{`, `}`, `[` and `]`, determine if the input string is valid (balanced).
A string is balanced if:
1. Open brackets are closed by the same type of brackets.
2. Open brackets are closed in the correct order.

### Intuition
When we encounter a closing bracket, it must match the most recently opened bracket that hasn't been closed yet. This "last in, first out" behavior perfectly matches the **Stack** data structure.

### Approach
1. Initialize an empty stack.
2. Iterate through each character in the string:
   - If the character is an opening bracket (`(`, `{`, `[`), push it onto the stack.
   - If the character is a closing bracket (`)`, `}`, `]`):
     - If the stack is empty, return `false` (no matching opening bracket).
     - Check the top element of the stack. If it matches the current closing bracket, pop it.
     - If it doesn't match, return `false`.
3. After the loop, if the stack is empty, return `true`. Otherwise, return `false` (some opening brackets were never closed).

### Complexity
- **Time Complexity**: **O(N)**, where N is the length of the string. We traverse the string once.
- **Space Complexity**: **O(N)** in the worst case (e.g., all opening brackets).

### Pattern
Stack-based matching.

### Common Mistakes
- **Empty Stack Check**: Forgetting to check if the stack is empty before popping when a closing bracket is encountered.
- **Final Stack Check**: Forgetting to check if the stack is empty after the loop (some brackets might remain unclosed).
- **Mismatched Types**: Not checking if the popped opening bracket actually matches the current closing bracket's type.

### Related Problems
- Longest Valid Parentheses
- Minimum Add to Make Parentheses Valid
- Generate Parentheses

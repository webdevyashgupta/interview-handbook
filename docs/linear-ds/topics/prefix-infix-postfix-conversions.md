# Prefix, Infix, and Postfix Conversions

### Problem Summary
Understand and convert between different arithmetic expression formats:
- **Infix**: Operator is between operands (e.g., `A + B`). This is how humans usually write.
- **Prefix (Polish Notation)**: Operator is before operands (e.g., `+ A B`).
- **Postfix (Reverse Polish Notation)**: Operator is after operands (e.g., `A B +`).

### Intuition
Computers prefer Postfix or Prefix because they remove the need for parentheses and clearly define the order of operations based on operator precedence. A stack is used to manage operators and ensure they are applied in the correct order.

### Approach

#### 1. Infix to Postfix
- Use a stack to store operators and a string to store the result.
- Iterate through the infix expression:
  - If operand: Add to result.
  - If `(`: Push to stack.
  - If `)`: Pop from stack to result until `(` is found.
  - If operator: Pop from stack to result while `precedence(stack.top) >= precedence(current)` and then push current operator.
- Pop remaining operators from stack to result.

#### 2. Infix to Prefix
- Reverse the infix expression (treat `(` as `)` and vice versa).
- Convert the reversed expression to postfix.
- Reverse the resulting postfix expression to get the prefix.

#### 3. Postfix to Infix / Prefix to Infix
- Use a stack of strings.
- If operand: Push to stack.
- If operator: Pop two operands, combine them with the operator in the correct order (with parentheses for infix), and push the result back.

## Implementation (Infix to Postfix)

```python
def infixToPostfix(exp: str) -> str:
    precedence = {'+': 1, '-': 1, '*': 2, '/': 2, '^': 3}
    stack = []
    res = ""
    
    for char in exp:
        if char.isalnum():
            res += char
        elif char == '(':
            stack.append(char)
        elif char == ')':
            while stack and stack[-1] != '(':
                res += stack.pop()
            stack.pop() # pop '('
        else:
            while stack and stack[-1] != '(' and precedence.get(char, 0) <= precedence.get(stack[-1], 0):
                res += stack.pop()
            stack.append(char)
            
    while stack:
        res += stack.pop()
        
    return res
```

### Complexity
- **Time Complexity**: **O(N)**, where N is the length of the expression.
- **Space Complexity**: **O(N)** for the stack.

### Pattern
Stack-based expression parsing.

### Common Mistakes
- **Precedence Rules**: Incorrectly defining or applying operator precedence (e.g., forgetting that `^` is higher than `*`).
- **Parentheses Handling**: Mismanaging brackets in the stack during conversion.
- **Right-to-Left vs Left-to-Right**: Forgetting that Prefix conversion often involves reversing the string or scanning from right to left.

### Related Problems
- Evaluate Postfix Expression
- Evaluate Prefix Expression
- Basic Calculator I, II, III

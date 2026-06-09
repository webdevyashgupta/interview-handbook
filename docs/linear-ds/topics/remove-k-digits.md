# Remove K Digits

## 🧩 Problem Summary
- **Problem**: Given a non-negative integer represented as a string `num` and an integer `k`, remove `k` digits from the number so that the new number is the smallest possible.
- **Input**: String `num`, Integer `k`.
- **Output**: The smallest possible number as a string.
- **Constraints**: 
    - The length of `num` is up to $10^5$.
    - `num` consists of only digits and does not contain leading zeros except for the number "0" itself.

## 💡 Intuition
- To make a number as small as possible, we want the smaller digits to be at the most significant positions (leftmost).
- If we see a sequence like `456`, and then a `2`, removing `6`, `5`, and `4` will make the number smaller because `2` is smaller than them.
- This is a **Greedy** approach combined with a **Monotonic Increasing Stack**.
- We traverse the number and for each digit, while the stack is not empty, `k > 0`, and the top of the stack is greater than the current digit, we pop from the stack and decrement `k`.

## 🔁 Approach

## Implementation

```python
def removeKdigits(num: str, k: int) -> str:
    stack = []
    
    for digit in num:
        while k > 0 and stack and stack[-1] > digit:
            stack.pop()
            k -= 1
        stack.append(digit)
    
    # If k > 0, remove from the end
    while k > 0:
        stack.pop()
        k -= 1
        
    # Build string and remove leading zeros
    res = "".join(stack).lstrip('0')
    
    return res if res else "0"
```

## ⏱️ Complexity
- **Time**: $O(N)$ where $N$ is the length of the string. Each digit is pushed and popped at most once.
- **Space**: $O(N)$ for the stack.

## 📌 Pattern
- Monotonic Stack (Greedy).

## ⚠️ Common Mistakes
- **Leading Zeros**: Forgetting to remove leading zeros after building the final result (e.g., `10200`, `k=1` should give `0200` $\to$ `200`).
- **Remaining K**: If the entire number is processed and `k` is still greater than 0, it means the stack is in non-decreasing order. We must remove the remaining `k` digits from the end of the stack.
- **Empty Result**: If all digits are removed, the answer should be "0", not an empty string.

## 🔗 Related Problems
- Next Greater Element
- Largest Rectangle in Histogram

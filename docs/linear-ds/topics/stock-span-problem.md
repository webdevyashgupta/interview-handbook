# Stock Span Problem

## 🧩 Problem Summary
- **Problem**: Design an algorithm that collects daily price quotes for a stock and returns the span of that stock's price for the current day. The span of the stock's price today is defined as the maximum number of consecutive days (starting from today and going backward) for which the price of the stock was less than or equal to today's price.
- **Input**: A stream of daily prices.
- **Output**: The span for each price.

## 💡 Intuition
- For a price on day `i`, we need to find the most recent day `j` (where `j < i`) such that `price[j] > price[i]`.
- The span is simply `i - j`.
- If no such day exists, the span is `i + 1` (it's the highest price seen so far).
- This is equivalent to finding the **Previous Greater Element (PGE)**.
- We can use a **Monotonic Decreasing Stack** to keep track of prices and their indices.

## 🔁 Approach

## Implementation

```python
class StockSpanner:
    def __init__(self):
        # Stack stores pairs: (price, index)
        self.stack = []
        self.index = -1

    def next(self, price: int) -> int:
        self.index += 1
        
        # Pop elements smaller than or equal to current price
        while self.stack and self.stack[-1][0] <= price:
            self.stack.pop()
            
        if not self.stack:
            ans = self.index + 1 # No PGE, span is from the start
        else:
            ans = self.index - self.stack[-1][1] # Current index - PGE index
            
        self.stack.append((price, self.index))
        return ans
```

## ⏱️ Complexity
- **Time**: $O(1)$ average per `next()` call. Each price is pushed and popped at most once.
- **Space**: $O(N)$ to store prices in the stack in the worst case (monotonically decreasing prices).

## 📌 Pattern
- Monotonic Stack (Previous Greater Element).

## ⚠️ Common Mistakes
- **Index Management**: Correctly calculating the difference between the current index and the index of the Previous Greater Element.
- **Equal Prices**: The problem states "less than or equal to today's price," so we must pop prices that are equal to the current price to include them in the span.

## 🔗 Related Problems
- Next Greater Element
- Online Stock Span (LeetCode)

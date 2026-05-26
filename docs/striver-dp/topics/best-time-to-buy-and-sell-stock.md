# Best Time to Buy and Sell Stock

## 🧩 Problem Summary
You are given an array `prices` where `prices[i]` is the price of a given stock on the $i^{th}$ day. You want to maximize your profit by choosing a **single day** to buy one stock and choosing a **different day in the future** to sell that stock.

### Input/Output
- **Input**: `prices = [7, 1, 5, 3, 6, 4]`
- **Output**: `5` (Buy on day 2 at price 1, sell on day 5 at price 6)

### Constraints
- $1 \le prices.length \le 10^5$
- $0 \le prices[i] \le 10^4$

## 💡 Intuition
To maximize profit with one transaction, we need to find the maximum difference between two prices, where the selling price occurs after the buying price. 
- For any day $i$, if we decide to sell on this day, we should have bought on the day with the **minimum price** between day $0$ and day $i-1$.
- We can maintain a running minimum of prices as we iterate through the array.

## 🔁 Approach

### 1. Greedy / 1D DP
As we iterate through the prices:
1. Update the `minPrice` encountered so far.
2. Calculate the potential `profit` if we sell today: `prices[i] - minPrice`.
3. Update `maxProfit` if the current profit is higher.

### 2. Time and Space
This approach only requires a single pass and a few variables.

## 🧠 DP State Definition
While often categorized as Greedy, it can be seen as a simple DP where `minPrice[i]` is the minimum price from day $0$ to $i$.
`maxProfit = max(maxProfit, prices[i] - minPrice)`

## 🔄 Recurrence Relation
`minPrice = min(minPrice, prices[i])`
`profit = prices[i] - minPrice`
`maxProfit = max(maxProfit, profit)`

## ⏱️ Complexity (Time & Space)
- **Time**: $O(N)$ - Single pass through the array.
- **Space**: $O(1)$ - Only a few variables (`minPrice`, `maxProfit`).

## 📌 Pattern
Stock DP (1 Transaction)

## ⚠️ Common Mistakes
- **Selling before buying**: Ensure the logic always subtracts a previous minimum from the current price.
- **Negative Profit**: If no profit is possible (prices only decrease), the answer should be `0`.

## 🔗 Related Problems
- Best Time to Buy and Sell Stock II
- Best Time to Buy and Sell Stock III
- Best Time to Buy and Sell Stock with Cooldown

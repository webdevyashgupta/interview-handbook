# Best Time to Buy and Sell Stock

## Problem Summary
You are given an array `prices` where `prices[i]` is the price of a given stock on the $i^{th}$ day. You want to maximize your profit by choosing a single day to buy one stock and choosing a different day in the future to sell that stock. Return the maximum profit you can achieve. If no profit can be made, return 0.

## Intuition
To maximize profit, you want to buy at the lowest possible price and sell at the highest possible price *after* the buying day.
- If you decide to sell on day `i`, you should have bought the stock at the minimum price seen between day `0` and day `i-1`.
- By keeping track of the minimum price seen so far as we iterate, we can calculate the potential profit for each day in a single pass.

## Approach

### Brute Force
- **Method**: Use two nested loops to check every possible pair of buy and sell days.
    - Outer loop `i` from `0` to `n-2` (buy day).
    - Inner loop `j` from `i+1` to `n-1` (sell day).
    - Calculate `prices[j] - prices[i]` and keep track of the maximum.
- **Time Complexity**: $O(N^2)$
- **Space Complexity**: $O(1)$

### Optimal Approach
- **Method**: 
    1. Initialize `mini = prices[0]` (minimum price seen so far).
    2. Initialize `maxProfit = 0`.
    3. Iterate through the array starting from the second element (index 1):
        - Calculate current `cost = prices[i] - mini`.
        - Update `maxProfit = max(maxProfit, cost)`.
        - Update `mini = min(mini, prices[i])`.
    4. Return `maxProfit`.
- **Space Complexity**: $O(1)$

## Implementation

```python
def maxProfit(prices):
    """
    Finds the maximum profit from buying and selling a stock once.
    Optimal Approach: Single Pass (Greedy/DP)
    Time Complexity: O(N), Space Complexity: O(1)
    """
    if not prices:
        return 0
        
    mini = prices[0] # Minimum price seen so far
    max_profit = 0
    
    for i in range(1, len(prices)):
        # Calculate potential profit if we sold today
        cost = prices[i] - mini
        max_profit = max(max_profit, cost)
        # Update minimum price for future sell days
        mini = min(mini, prices[i])
        
    return max_profit
```

## Complexity
- **Time**: $O(N)$ - A single pass through the array.
- **Space**: $O(1)$ - Only two variables (`mini` and `maxProfit`) are used.

## Pattern
- **Dynamic Programming / Greedy**: We "remember" the minimum price from the past to make an optimal decision at the current step.

## Common Mistakes
- **Selling before Buying**: Ensure you only calculate profit for days *after* your current minimum price candidate.
- **Initialization**: Initializing `maxProfit` with a value other than 0 (since you can choose not to trade if prices only decrease).

## Related Problems
- Best Time to Buy and Sell Stock II (Multiple transactions).
- Best Time to Buy and Sell Stock III (At most two transactions).
- Best Time to Buy and Sell Stock with Cooldown.

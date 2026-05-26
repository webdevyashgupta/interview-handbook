# Count Partitions With Given Difference (DP on Subsequences)

## 🧩 Problem Summary
- **Problem:** Given an array `arr` and an integer `D`, count the number of ways to partition the array into two subsets $S_1$ and $S_2$ such that $sum(S_1) - sum(S_2) = D$.
- **Input:** Array `arr`, difference `D`.
- **Output:** Total number of such partitions.

## 💡 Intuition
- **Mathematical Derivation:**
  1. $s_1 + s_2 = \text{TotalSum}$
  2. $s_1 - s_2 = D$
  3. Adding (1) and (2): $2s_1 = \text{TotalSum} + D$
  4. $\implies s_1 = \frac{\text{TotalSum} + D}{2}$
- **The Transformation:** The problem is reduced to finding the number of subsets whose sum is exactly $\frac{\text{TotalSum} + D}{2}$.
- **Edge Cases:**
  1. $(\text{TotalSum} + D)$ must be even because $s_1$ must be an integer.
  2. $\text{TotalSum} \ge D$ because subset sums cannot be negative.

## 🔁 Approach

### 1. Unified Logic (Using Count Subsets with Sum K)
- **Explanation:** Calculate the target $s_1$ and call the "Count Subsets with Sum K" function.
- **Code (Python):**
```python
def countPartitionsWithDiff(arr, d):
    n = len(arr)
    total_sum = sum(arr)
    
    # Check if a valid partition sum s1 exists
    if (total_sum + d) % 2 != 0 or total_sum < d:
        return 0
    
    target = (total_sum + d) // 2
    return findWays(arr, target) # Reusing the Count Subsets function

def findWays(arr, k):
    n = len(arr)
    # Important: Handling zeros correctly is crucial here
    prev = [0] * (k + 1)
    
    if arr[0] == 0:
        prev[0] = 2 # Pick 0 or Not-pick 0
    else:
        prev[0] = 1 # Not-pick 0
        
    if arr[0] != 0 and arr[0] <= k:
        prev[arr[0]] = 1 # Pick arr[0]
        
    for i in range(1, n):
        cur = [0] * (k + 1)
        for target in range(0, k + 1):
            not_pick = prev[target]
            pick = 0
            if arr[i] <= target:
                pick = prev[target - arr[i]]
            cur[target] = (pick + not_pick) % (10**9 + 7)
        prev = cur
            
    return prev[k]
```

## 🧠 DP State Definition
- `dp[i][target]` represents the number of subsets of the first `i` elements that sum up to `target`.

## ⏱️ Complexity
- **Time Complexity:** $O(N \times \text{Target})$ where $\text{Target} = (\text{TotalSum} + D) / 2$.
- **Space Complexity:** $O(\text{Target})$ with space optimization.

## 📌 Pattern
- **DP on Subsequences:** Converting a partition problem into a target sum problem.

## ⚠️ Common Mistakes
- **TotalSum < D:** If the required difference is greater than the total sum of elements, no such partition exists.
- **Odd Sum:** If `TotalSum + D` is odd, you cannot divide it by 2 to get an integer sum, so return 0.
- **Handling Zeros:** Ensure your `findWays` function correctly counts subsets when elements are 0.

## 🔗 Related Problems
- [Count Subsets with Sum K](./count-subsets-with-sum-k.md)
- [Target Sum (LeetCode)](https://leetcode.com/problems/target-sum/) - This is exactly the same problem!

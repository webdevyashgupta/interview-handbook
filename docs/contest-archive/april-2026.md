# Contest Analysis: April 2026

## 🗓️ Weekly Contest 499 (April 26, 2026)

### Q1. Width of Columns in Grid
**Difficulty:** 🟢 Easy | **Pattern:** Array / Matrix

**Intuition:**
Find the maximum width (number of digits) of any element in each column.

**Implementation:**
```python
def columnWidths(grid):
    m, n = len(grid), len(grid[0])
    res = []
    for j in range(n):
        max_w = 0
        for i in range(m):
            max_w = max(max_w, len(str(grid[i][j])))
        res.append(max_w)
    return res
```

### Q2. Score All Prefixes
**Difficulty:** 🟡 Medium | **Pattern:** Array / Prefix Sum

**Intuition:**
The score of a prefix is defined as its sum plus the maximum element in that prefix. We need to compute this for all prefixes.

**Implementation:**
```python
def scorePrefixes(nums):
    n = len(nums)
    res = []
    curr_sum = 0
    curr_max = 0
    for x in nums:
        curr_sum += x
        curr_max = max(curr_max, x)
        res.append(curr_sum + curr_max)
    return res
```

### Q3. Minimize Maximum of Array II
**Difficulty:** 🟡 Medium | **Pattern:** Binary Search on Answer / Greedy

**Intuition:**
Can we make the maximum element in the array at most $X$? We can move values from $nums[i]$ to $nums[i-1]$. This is a classic "minimize maximum" problem solvable by binary search.

**Implementation:**
```python
def minimizeMax(nums):
    def check(limit):
        extra = 0
        for x in reversed(nums):
            if x > limit:
                extra += (x - limit)
            else:
                # Can take some extra from right
                can_take = limit - x
                extra -= min(extra, can_take)
        return extra == 0

    low, high = 0, max(nums)
    ans = high
    while low <= high:
        mid = (low + high) // 2
        if check(mid):
            ans = mid
            high = mid - 1
        else:
            low = mid + 1
    return ans
```

### Q4. Ways to Earn Points
**Difficulty:** 🔴 Hard | **Pattern:** Dynamic Programming / Knapsack

**Intuition:**
This is a variation of the bounded knapsack problem. We have different types of questions, each with a count and a point value. We want to find the number of ways to reach exactly $T$ points.

**Implementation:**
```python
def countWays(target, types):
    MOD = 10**9 + 7
    dp = [0] * (target + 1)
    dp[0] = 1
    
    for count, points in types:
        for t in range(target, 0, -1):
            for k in range(1, count + 1):
                if t - k * points >= 0:
                    dp[t] = (dp[t] + dp[t - k * points]) % MOD
                else:
                    break
    return dp[target]
```

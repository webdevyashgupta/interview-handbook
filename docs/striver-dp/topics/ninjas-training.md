# Ninja's Training

## 🧩 Problem Summary
- **Problem**: A ninja has a training schedule for $N$ days. Each day, there are 3 activities (Running, Fighting, Learning). Each activity has merit points. The ninja cannot perform the same activity on two consecutive days.
- **Input**: An $N \times 3$ matrix `points`.
- **Output**: Maximum merit points the ninja can earn.
- **Constraints**: $N$ days, 3 activities per day.

## 💡 Intuition
- On any given day, the choice of activity depends on what was done the day before.
- **Greedy Fails**: Picking the max points today might force us to pick a very small value tomorrow.
- **Example**: 
    - Day 0: [10, 50, 1]
    - Day 1: [5, 100, 11]
    - Greedy picks 50 on Day 0, which blocks 100 on Day 1. Total = $50 + 11 = 61$.
    - Optimal picks 10 on Day 0, then 100 on Day 1. Total = $10 + 100 = 110$.
- **Conclusion**: We must explore all paths and use DP to handle overlapping subproblems.

## 🔁 Approach

### 1. Recursion
- **Explanation**: `f(day, last_task)` returns max points from day 0 to `day` given that `last_task` was performed on `day + 1`.
- **Code (Python)**:
```python
def solve(day, last, points):
    if day == 0:
        maxi = 0
        for i in range(3):
            if i != last:
                maxi = max(maxi, points[0][i])
        return maxi
    
    maxi = 0
    for i in range(3):
        if i != last:
            p = points[day][i] + solve(day - 1, i, points)
            maxi = max(maxi, p)
    return maxi
```

### 2. Memoization (Top-Down DP)
- **Explanation**: Use a 2D DP table `dp[N][4]` to store states. `last` ranges from 0-2 (activities) and 3 (initial state - no activity performed yet).
- **Code (Python)**:
```python
def ninjaTraining(n, points):
    dp = [[-1] * 4 for _ in range(n)]

    def solve(day, last):
        if day == 0:
            maxi = 0
            for i in range(3):
                if i != last:
                    maxi = max(maxi, points[0][i])
            return maxi
        
        if dp[day][last] != -1:
            return dp[day][last]
        
        maxi = 0
        for i in range(3):
            if i != last:
                p = points[day][i] + solve(day - 1, i)
                maxi = max(maxi, p)
        
        dp[day][last] = maxi
        return dp[day][last]

    return solve(n - 1, 3)
```

### 3. Tabulation (Bottom-Up DP)
- **Explanation**: Fill the table day by day.
- **Code (Python)**:
```python
def ninjaTraining(n, points):
    dp = [[0] * 4 for _ in range(n)]
    
    # Base case for Day 0
    dp[0][0] = max(points[0][1], points[0][2])
    dp[0][1] = max(points[0][0], points[0][2])
    dp[0][2] = max(points[0][0], points[0][1])
    dp[0][3] = max(points[0][0], max(points[0][1], points[0][2]))
    
    for day in range(1, n):
        for last in range(4):
            dp[day][last] = 0
            for task in range(3):
                if task != last:
                    point = points[day][task] + dp[day-1][task]
                    dp[day][last] = max(dp[day][last], point)
                    
    return dp[n-1][3]
```

### 4. Space Optimization
- **Explanation**: We only need the results from `day - 1` to calculate `day`.
- **Code (Python)**:
```python
def ninjaTraining(n, points):
    prev = [0] * 4
    
    prev[0] = max(points[0][1], points[0][2])
    prev[1] = max(points[0][0], points[0][2])
    prev[2] = max(points[0][0], points[0][1])
    prev[3] = max(points[0][0], points[0][1], points[0][2])
    
    for day in range(1, n):
        temp = [0] * 4
        for last in range(4):
            temp[last] = 0
            for task in range(3):
                if task != last:
                    temp[last] = max(temp[last], points[day][task] + prev[task])
        prev = temp
        
    return prev[3]
```

## 🧠 DP State Definition
- `dp[day][last]` = Maximum merit points earned up to `day` given that the activity performed on `day + 1` was `last`.

## 🔄 Recurrence Relation
- $f(day, last) = \max_{i \in \{0, 1, 2\}, i \neq last} \{ points[day][i] + f(day-1, i) \}$

## ⏱️ Complexity
- **Time**: $O(N \times 4 \times 3) \approx O(N)$
- **Space**: $O(N \times 4)$ (Memoization/Tabulation) or $O(4)$ (Space Optimized)

## 📌 Pattern
- **2D DP**: DP on states/activities.

## ⚠️ Common Mistakes
- Not handling the base case (Day 0) correctly for all possible `last` activities.
- Forgetting that `last` can be 3 (to represent no task done on the "next" day when starting).

## 🔗 Related Problems
- [Vacation (AtCoder)](https://atcoder.jp/contests/dp/tasks/dp_c)
- [House Robber](https://leetcode.com/problems/house-robber/)

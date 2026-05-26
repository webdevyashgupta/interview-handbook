# 🚀 Ultra-Concise DP Cheatsheet

## 🛠️ The DP Framework
1. **Identify**: Overlapping subproblems & Optimal substructure.
2. **State**: Define `dp[i]` (or `dp[i][j]`) in plain English.
3. **Recurrence**: Express `dp[i]` in terms of smaller states.
4. **Base Cases**: Smallest subproblems (e.g., `dp[0]`, `dp[1]`).
5. **Optimize**: Memoization → Tabulation → Space Optimization.

---

## 📊 DP Patterns Summary
| Pattern | Example Problems | State Definition | Recurrence Logic |
| :--- | :--- | :--- | :--- |
| **1D DP** | Climbing Stairs, Frog Jump | `dp[i]`: Result at index `i` | `f(i) = f(i-1) + f(i-2)` |
| **Grid DP** | Unique Paths, Min Path Sum | `dp[i][j]`: Result at cell `(i,j)` | `f(i,j) = min/sum(f(i-1,j), f(i,j-1))` |
| **Subsequences** | Knapsack, Subset Sum | `dp[i][target]`: Using first `i` items | `f(i,T) = f(i-1,T) \| f(i-1, T-arr[i])` |
| **Strings** | LCS, Edit Distance | `dp[i][j]`: Prefixes `S1[0..i]`, `S2[0..j]` | `if S1[i]==S2[j]: match; else: try options` |
| **Stocks** | Buy/Sell Stocks I-IV | `dp[i][buy][cap]`: Day `i`, state `buy` | `f(i,1) = max(-pr[i] + f(i+1,0), f(i+1,1))` |
| **Partition** | MCM, Burst Balloons | `dp[i][j]`: Optimal result for `[i...j]` | `f(i,j) = min(f(i,k) + f(k+1,j) + cost)` |

---

## ⚡ Key Transitions & Templates

### 1. Subsequences (Pick / Non-Pick)
```python
# Base Case: target == 0 or index < 0
not_pick = solve(ind - 1, target)
pick = False
if arr[ind] <= target:
    pick = solve(ind - 1, target - arr[ind])
return pick or not_pick
```

### 2. Strings (Match / No Match)
```python
if S1[i] == S2[j]:
    return 1 + solve(i-1, j-1) # LCS example
else:
    return max(solve(i-1, j), solve(i, j-1))
```

### 3. Partition DP (Range Loop)
```python
for k in range(i, j):
    steps = f(i, k) + f(k + 1, j) + (arr[i-1] * arr[k] * arr[j])
    mini = min(mini, steps)
```

---

## ⏱️ Complexity Reference
| Problem Type | Time Complexity | Space (Tabulation) | Space (Optimized) |
| :--- | :--- | :--- | :--- |
| **1D DP** | $O(N)$ | $O(N)$ | $O(1)$ |
| **Grid DP** | $O(M \times N)$ | $O(M \times N)$ | $O(N)$ |
| **Subsequences** | $O(N \times Target)$ | $O(N \times Target)$ | $O(Target)$ |
| **Strings** | $O(N \times M)$ | $O(N \times M)$ | $O(M)$ |
| **MCM / Partition** | $O(N^3)$ | $O(N^2)$ | $O(N^2)$ |

---

## 💡 Key Tricks & Optimization

### 1. Space Optimization (The "Two-Row" Trick)
If `dp[i]` only depends on `dp[i-1]`, replace the 2D table with two 1D arrays (`prev` and `curr`).
- **Grid**: `curr[j]` depends on `prev[j]` (up) and `curr[j-1]` (left).
- **Subsequences**: If you only need the previous row, `O(Target)` space is enough.

### 2. Index Shifting for Strings
To handle base cases (empty strings) easily in tabulation, use `1-based` indexing:
- `dp[i][j]` represents `S1[i-1]` and `S2[j-1]`.
- Row 0 and Col 0 represent empty string states.

### 3. Unbounded Knapsack (Infinite Supply)
Instead of `prev` and `curr`, use a **single row** updated from left to right.
- `dp[target] = max(dp[target], val + dp[target - weight])`

### 4. Backtracking to Find Solution
To print the LCS or Shortest Path:
- Start from `dp[n][m]` and move towards `dp[0][0]` by checking which neighbor produced the current value.

### 5. Recursion Stack Limit
For Python, always use `sys.setrecursionlimit(2000)` or prefer tabulation for very deep trees.

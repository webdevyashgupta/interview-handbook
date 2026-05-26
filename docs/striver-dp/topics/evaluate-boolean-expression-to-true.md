# Evaluate Boolean Expression to True (Partition DP)

## 🧩 Problem Summary
- **Problem:** Given a boolean expression with operands `T` (True) and `F` (False) and operators `&` (AND), `|` (OR), and `^` (XOR). Count the number of ways to parenthesize the expression such that it evaluates to `True`.
- **Example:** `T ^ F | T`
  - `(T ^ F) | T` -> `T | T` -> `True`
  - `T ^ (F | T)` -> `T ^ T` -> `False`
  - Total ways = 1.

## 💡 Intuition
- **Partitioning:** Like Matrix Chain Multiplication, we can partition the expression at every operator.
- **Multiple Results:** To calculate the number of ways a sub-expression evaluates to `True`, we often need to know the number of ways it evaluates to `False` (e.g., for XOR: `True ^ False = True`, `False ^ True = True`).
- **DP State:** We need a 3D DP state `f(i, j, isTrue)` representing the number of ways the sub-expression from index `i` to `j` evaluates to `isTrue` (1 for True, 0 for False).

## 🔁 Approach

### 1. Recursion with Memoization
- **Explanation:** Iterate through all operators in the current range `[i, j]`. For each operator, calculate the number of ways to get `True` and `False` from the left and right sub-expressions.
- **Code (C++):**
```cpp
long long solve(int i, int j, int isTrue, string& s, vector<vector<vector<long long>>>& dp) {
    if (i > j) return 0;
    if (i == j) {
        if (isTrue) return s[i] == 'T';
        else return s[i] == 'F';
    }
    if (dp[i][j][isTrue] != -1) return dp[i][j][isTrue];

    long long ways = 0;
    for (int k = i + 1; k <= j - 1; k += 2) {
        long long lT = solve(i, k - 1, 1, s, dp);
        long long lF = solve(i, k - 1, 0, s, dp);
        long long rT = solve(k + 1, j, 1, s, dp);
        long long rF = solve(k + 1, j, 0, s, dp);

        if (s[k] == '&') {
            if (isTrue) ways = (ways + (lT * rT)) % 1000000007;
            else ways = (ways + (lT * rF) + (lF * rT) + (lF * rF)) % 1000000007;
        } else if (s[k] == '|') {
            if (isTrue) ways = (ways + (lT * rT) + (lT * rF) + (lF * rT)) % 1000000007;
            else ways = (ways + (lF * rF)) % 1000000007;
        } else if (s[k] == '^') {
            if (isTrue) ways = (ways + (lT * rF) + (lF * rT)) % 1000000007;
            else ways = (ways + (lT * rT) + (lF * rF)) % 1000000007;
        }
    }
    return dp[i][j][isTrue] = ways;
}

int countWays(int n, string s) {
    vector<vector<vector<long long>>> dp(n, vector<vector<long long>>(n, vector<long long>(2, -1)));
    return solve(0, n - 1, 1, s, dp);
}
```

## 🧠 DP State Definition
- `dp[i][j][isTrue]` is the number of ways the expression from index `i` to `j` evaluates to `isTrue` (where `isTrue` is 0 or 1).

## ⏱️ Complexity
- **Time Complexity:** $O(2 \times N^3)$ where $N$ is the length of the string. The factor of 2 comes from the `isTrue` state.
- **Space Complexity:** $O(2 \times N^2)$ for the DP table + $O(N)$ for auxiliary stack space.

## 📌 Pattern
- **Partition DP:** Breaking the expression at each operator and combining results of sub-expressions.

## ⚠️ Common Mistakes
- **Operator Selection:** Operators are only at odd indices if indices start from 0 (T or F at 0, 2, 4...). Ensure the loop `k` iterates over operators only.
- **Missing Cases:** Forgetting that "False" ways are needed to calculate "True" ways for some operators (like XOR and OR).
- **Modulo:** Intermediate products can exceed `int` range, use `long long` and apply modulo at each addition.

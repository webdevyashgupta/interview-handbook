# Minimum Insertions to Make String Palindrome

## 🧩 Problem Summary
- **Problem:** Given a string `S`, find the minimum number of insertions needed to make it a palindrome. You can insert characters anywhere in the string.
- **Input:** String `S`.
- **Output:** Minimum number of insertions.

## 💡 Intuition
- To make a string a palindrome with minimum insertions, we should keep the longest existing palindromic part of the string intact.
- The longest existing palindromic part is the **Longest Palindromic Subsequence (LPS)**.
- Any character that is **not** part of the LPS will need a corresponding character inserted to its mirror position to form a palindrome.
- Therefore, the number of insertions required is the total length of the string minus the length of the LPS.

## 🔁 Approach

### 1. Using LPS (Longest Palindromic Subsequence)
- **Logic:**
  1. Find the length of the LPS of the given string `S`.
  2. LPS can be found by `LCS(S, reverse(S))`.
  3. Answer = `len(S) - LPS(S)`.
- **Code (Python):**
```python
def lcs(s1, s2):
    n, m = len(s1), len(s2)
    prev = [0] * (m + 1)
    for i in range(1, n + 1):
        cur = [0] * (m + 1)
        for j in range(1, m + 1):
            if s1[i-1] == s2[j-1]:
                cur[j] = 1 + prev[j-1]
            else:
                cur[j] = max(prev[j], cur[j-1])
        prev = cur
    return prev[m]

def min_insertions_to_make_palindrome(s):
    n = len(s)
    lps_length = lcs(s, s[::-1])
    return n - lps_length
```

## 🧠 DP State Definition
- `dp[i][j]` (from LCS) is the length of the longest common subsequence between `S` and its reverse.

## ⏱️ Complexity
- **Time Complexity:** $O(N^2)$ where $N$ is the length of the string.
- **Space Complexity:** $O(N)$ with space-optimized LCS.

## 📌 Pattern
- **LPS Variation:** Many palindrome problems revolve around finding the LPS.

## ⚠️ Common Mistakes
- **Incorrect Formula:** Thinking the answer is just `len(S) // 2` or something similar.
- **Not using LCS:** Trying to solve it with a more complex recursion when it directly maps to LCS.

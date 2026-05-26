# Longest Palindromic Subsequence

## 🧩 Problem Summary
- **Problem:** Given a string `S`, find the length of the longest subsequence that is also a palindrome.
- **Input:** String `S`.
- **Output:** Length of the Longest Palindromic Subsequence (LPS).

## 💡 Intuition
- A palindrome is a string that reads the same forwards and backwards.
- If we reverse the string `S` to get `S_reverse`, then any common subsequence between `S` and `S_reverse` will be palindromic in nature.
- To find the **longest** such palindromic subsequence, we simply need to find the **Longest Common Subsequence (LCS)** of `S` and `reverse(S)`.

## 🔁 Approach

### 1. LCS of S and Reverse(S)
- **Logic:**
  1. Let `S1 = S`.
  2. Let `S2 = reverse(S)`.
  3. Return `LCS(S1, S2)`.
- **Code (Python):**
```python
def longest_common_subsequence(s1, s2):
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

def longest_palindromic_subsequence(s):
    s_rev = s[::-1]
    return longest_common_subsequence(s, s_rev)
```

## 🧠 DP State Definition
- `dp[i][j]` is the length of the LCS of `S` and `reverse(S)`.

## ⏱️ Complexity
- **Time Complexity:** $O(N^2)$ where $N$ is the length of the string.
- **Space Complexity:** $O(N)$ with space-optimized LCS.

## 📌 Pattern
- **Problem Transformation:** Reducing a new problem (LPS) to a known problem (LCS).

## ⚠️ Common Mistakes
- **Confusing Subsequence with Substring:** Palindromic subsequence characters don't need to be adjacent in the original string.
- **Not Reversing:** Forgetting that LPS is strictly LCS of the string and its reverse.

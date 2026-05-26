# Minimum Insertions/Deletions to Convert String A to String B

## 🧩 Problem Summary
- **Problem:** Given two strings `S1` and `S2`, find the minimum number of operations (insertions and deletions) required to convert `S1` to `S2`.
- **Operations Allowed:**
  - Delete a character from `S1`.
  - Insert a character into `S1`.
- **Input:** String `S1`, String `S2`.
- **Output:** Total minimum number of operations.

## 💡 Intuition
- To convert `S1` to `S2` in the minimum number of steps, we should identify the largest part of `S1` that is already present in `S2` and keep it intact.
- This "largest common part" is the **Longest Common Subsequence (LCS)**.
- **Deletions:** Any character in `S1` that is not part of the LCS must be deleted.
  - `Deletions = len(S1) - LCS_length`
- **Insertions:** Any character in `S2` that is not part of the LCS must be inserted into `S1`.
  - `Insertions = len(S2) - LCS_length`
- **Total Operations:** `Deletions + Insertions`.

## 🔁 Approach

### 1. Using LCS (Longest Common Subsequence)
- **Logic:**
  1. Calculate `LCS_length` of `S1` and `S2`.
  2. `total_ops = (len(S1) - LCS_length) + (len(S2) - LCS_length)`
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

def min_operations(s1, s2):
    n, m = len(s1), len(s2)
    k = lcs(s1, s2)
    return (n - k) + (m - k)
```

## 🧠 DP State Definition
- `dp[i][j]` (from LCS) is the length of the longest common subsequence between `S1[0...i-1]` and `S2[0...j-1]`.

## ⏱️ Complexity
- **Time Complexity:** $O(N \times M)$ where $N$ and $M$ are lengths of the strings.
- **Space Complexity:** $O(\min(N, M))$ with space-optimized LCS.

## 📌 Pattern
- **LCS Application:** Keeping the common part intact to minimize changes.

## ⚠️ Common Mistakes
- **Total Operations Formula:** Forgetting that it involves both deletions from the source and insertions to match the target.
- **Consecutive Substring:** Confusing LCS (Subsequence) with Longest Common Substring. Operations can be done anywhere, so we use LCS.

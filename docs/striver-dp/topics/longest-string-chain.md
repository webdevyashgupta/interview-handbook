# Longest String Chain

## 🧩 Problem Summary
- **Problem**: Given a list of words, find the length of the longest possible word chain.
- **Word Chain**: A sequence of words $[word_1, word_2, \dots, word_k]$ where $word_i$ is a predecessor of $word_{i+1}$ for all $1 \le i < k$.
- **Predecessor**: $word_1$ is a predecessor of $word_2$ if and only if we can insert exactly one character anywhere in $word_1$ to make it equal to $word_2$.
- **Input**: An array of strings `words`.
- **Output**: The length of the longest string chain.

## 💡 Intuition
1. **Relationship**: This is another variation of the **Longest Increasing Subsequence (LIS)**. Instead of numbers being greater, we check if one string can be formed from another by adding one character.
2. **Sorting**: To ensure we only look at potential predecessors, we should **sort the words by their lengths**. A word of length $L$ can only have a predecessor of length $L-1$.
3. **LIS with Custom Comparison**: Once sorted, we use the $O(N^2)$ LIS algorithm but replace the `nums[i] > nums[j]` condition with a custom `isPredecessor(s1, s2)` function.

## 🔁 Approach

### 1. Custom Comparison (Two Pointers)
- `isPredecessor(s1, s2)`:
    - Check if `len(s2) == len(s1) + 1`.
    - Use two pointers to verify if `s2` contains all characters of `s1` in order, with exactly one extra character.

### 2. Implementation (Python)
```python
def longestStrChain(words):
    def isPredecessor(s1, s2):
        if len(s2) != len(s1) + 1:
            return False
        first, second = 0, 0
        while second < len(s2):
            if first < len(s1) and s1[first] == s2[second]:
                first += 1
                second += 1
            else:
                second += 1
        return first == len(s1)

    words.sort(key=len)
    n = len(words)
    dp = [1] * n
    max_len = 1
    
    for i in range(n):
        for j in range(i):
            if isPredecessor(words[j], words[i]) and dp[i] < dp[j] + 1:
                dp[i] = dp[j] + 1
        max_len = max(max_len, dp[i])
        
    return max_len
```

## 🧠 DP State Definition
- `dp[i]` = Length of the longest string chain ending at index `i` (after sorting by length).

## 🔄 Recurrence Relation
- $dp[i] = \max(1, 1 + dp[j])$ for all $j < i$ such that `isPredecessor(words[j], words[i])` is true.

## ⏱️ Complexity
- **Time**: $O(N^2 \cdot L)$, where $N$ is the number of words and $L$ is the maximum length of a word. The $L$ factor comes from the `isPredecessor` check.
- **Space**: $O(N)$ for the `dp` array.

## 📌 Pattern
- **LIS on Strings**: Sorting by a property (length) and applying the LIS structure.

## ⚠️ Common Mistakes
- **Not sorting by length**: The chain must follow increasing lengths. Standard lexicographical sort is not enough.
- **Incorrect predecessor logic**: Ensure the two-pointer logic handles insertions at any position (beginning, middle, or end).

## 🔗 Related Problems
- [Largest Divisible Subset](https://leetcode.com/problems/largest-divisible-subset/)
- [Longest Increasing Subsequence](https://leetcode.com/problems/longest-increasing-subsequence/)

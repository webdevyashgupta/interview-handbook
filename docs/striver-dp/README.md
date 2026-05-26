# 📘 Striver DP Notes

Welcome to the comprehensive guide for Dynamic Programming, based on the popular A-Z DP series by Striver (Take U Forward). This repository contains detailed notes, approach explanations, and optimized solutions for various DP patterns.

## 🚀 Quick Links
- [📁 DP Patterns](./patterns/dp-patterns.md) - Common DP patterns and how to identify them.
- [📝 DP Templates](./templates/dp-templates.md) - Ready-to-use code templates for Recursion, Memoization, and Tabulation.
- [📋 Cheatsheet](./cheatsheet/dp-cheatsheet.md) - A quick reference guide for space and time complexities.

---

## 📚 Topics Covered

### 1. Introduction
- [Introduction to DP](./topics/introduction-to-dp.md)

### 2. 1D Dynamic Programming
- [Climbing Stairs](./topics/climbing-stairs.md)
- [Frog Jump](./topics/frog-jump.md)
- [Frog Jump with K Distance](./topics/frog-jump-with-k-distance.md)
- [Maximum Sum of Non-Adjacent Elements](./topics/maximum-sum-of-non-adjacent-elements.md)
- [House Robber 2](./topics/house-robber-2.md)

### 3. 2D/3D DP on Grids
- [Ninja's Training](./topics/ninjas-training.md)
- [Grid Unique Paths](./topics/grid-unique-paths.md)
- [Unique Paths 2 (Obstacles)](./topics/unique-paths-2.md)
- [Minimum Path Sum in Grid](./topics/minimum-path-sum-in-grid.md)
- [Triangle DP](./topics/triangle-dp.md)
- [Minimum/Maximum Falling Path Sum](./topics/minimum-maximum-falling-path-sum.md)
- [Cherry Pickup II (3D DP)](./topics/cherry-pickup-ii.md)

### 4. DP on Subsequences
- [Subset Sum Equals Target](./topics/subset-sum-equals-target.md)
- [Partition Equal Subset Sum](./topics/partition-equal-subset-sum.md)
- [Partition Set into Two Subsets with Minimum Absolute Difference](./topics/partition-set-into-two-subsets-with-minimum-absolute-sum-difference.md)
- [Count Subsets with Sum K](./topics/count-subsets-with-sum-k.md)
- [Count Partitions with Given Difference](./topics/count-partitions-with-given-difference.md)
- [0-1 Knapsack](./topics/0-1-knapsack.md)
- [Minimum Coins](./topics/minimum-coins.md)
- [Target Sum](./topics/target-sum.md)
- [Coin Change 2 (Infinite Supply)](./topics/coin-change-2.md)
- [Unbounded Knapsack](./topics/unbounded-knapsack.md)
- [Rod Cutting Problem](./topics/rod-cutting-problem.md)

### 5. DP on Strings
- [Longest Common Subsequence](./topics/longest-common-subsequence.md)
- [Print Longest Common Subsequence](./topics/print-longest-common-subsequence.md)
- [Longest Common Substring](./topics/longest-common-substring.md)
- [Longest Palindromic Subsequence](./topics/longest-palindromic-subsequence.md)
- [Minimum Insertions to Make String Palindrome](./topics/minimum-insertions-to-make-string-palindrome.md)
- [Minimum Insertions/Deletions to Convert String](./topics/minimum-insertions-deletions-to-convert-string.md)
- [Shortest Common Supersequence](./topics/shortest-common-supersequence.md)
- [Distinct Subsequences](./topics/distinct-subsequences.md)
- [Edit Distance](./topics/edit-distance.md)
- [Wildcard Matching](./topics/wildcard-matching.md)

### 6. DP on Stocks
- [Best Time to Buy and Sell Stock](./topics/best-time-to-buy-and-sell-stock.md)
- [Buy and Sell Stock II](./topics/buy-and-sell-stock-ii.md)
- [Buy and Sell Stock III](./topics/buy-and-sell-stocks-iii.md)
- [Buy and Sell Stock IV](./topics/buy-and-sell-stocks-iv.md)
- [Buy and Sell Stock with Cooldown](./topics/buy-and-sell-stocks-with-cooldown.md)
- [Buy and Sell Stock with Transaction Fee](./topics/buy-and-sell-stocks-with-transaction-fee.md)

### 7. DP on LIS (Longest Increasing Subsequence)
- [Longest Increasing Subsequence](./topics/longest-increasing-subsequence.md)
- [Printing Longest Increasing Subsequence](./topics/printing-longest-increasing-subsequence.md)
- [LIS using Binary Search](./topics/longest-increasing-subsequence-binary-search.md)
- [Largest Divisible Subset](./topics/largest-divisible-subset.md)
- [Longest String Chain](./topics/longest-string-chain.md)
- [Longest Bitonic Subsequence](./topics/longest-bitonic-subsequence.md)
- [Number of Longest Increasing Subsequences](./topics/number-of-longest-increasing-subsequences.md)

### 8. Partition DP
- [Matrix Chain Multiplication](./topics/matrix-chain-multiplication.md)
- [Minimum Cost to Cut the Stick](./topics/minimum-cost-to-cut-the-stick.md)
- [Burst Balloons](./topics/burst-balloons.md)
- [Evaluate Boolean Expression to True](./topics/evaluate-boolean-expression-to-true.md)
- [Palindrome Partitioning II](./topics/palindrome-partitioning-ii.md)
- [Partition Array for Maximum Sum](./topics/partition-array-for-maximum-sum.md)

### 9. DP on Rectangles
- [Maximum Rectangle Area with all 1s](./topics/maximum-rectangle-area-with-all-1s.md)
- [Count Square Submatrices with all Ones](./topics/count-square-submatrices-with-all-ones.md)

---

## 📖 How to Use This Guide

To master Dynamic Programming, follow this systematic approach for each problem:

1.  **Identify the DP Pattern**: Use the [Patterns](./patterns/dp-patterns.md) guide to recognize the type of problem.
2.  **Start with Recursion**: Express the problem in terms of indices and try out all possible choices at each index.
3.  **Memoize (Top-Down)**: Store the results of subproblems in an array/map to avoid redundant calculations.
4.  **Tabulate (Bottom-Up)**: Convert the recursive logic into an iterative approach using a DP table.
5.  **Space Optimization**: If the current state only depends on the previous row/states, optimize the space complexity from $O(N^2)$ to $O(N)$ or $O(1)$.

### Study Sequence Recommendation:
1D DP $\rightarrow$ Grids $\rightarrow$ Subsequences $\rightarrow$ Strings $\rightarrow$ Stocks $\rightarrow$ LIS $\rightarrow$ Partition DP.

---
*Happy Coding!* 💻

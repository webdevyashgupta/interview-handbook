# Dynamic Programming Patterns Guide

This guide categorizes common Dynamic Programming (DP) problems into patterns. Mastering these patterns helps in identifying and solving new problems efficiently.

---

## 1. 1D DP
**Description:** Problems involving a linear sequence where the state depends on one or more previous states (usually `i-1`, `i-2`).
- **पहचान (Identification):**
    - "Count the number of ways to reach..."
    - "Find the minimum/maximum cost to reach the end..."
    - Constraint: Cannot pick adjacent elements.
- **Example Problems:**
    - [Climbing Stairs](../topics/climbing-stairs.md)
    - [Frog Jump](../topics/frog-jump.md)
    - [Maximum Sum of Non-Adjacent Elements](../topics/maximum-sum-of-non-adjacent-elements.md)
    - [House Robber 2](../topics/house-robber-2.md)

---

## 2. 2D/Grid DP
**Description:** Problems involving a grid (matrix) where you need to find a path or a property based on grid movement.
- **पहचान (Identification):**
    - Movement restricted (e.g., only right and down).
    - Fixed or variable starting and ending points.
    - Grid with obstacles.
- **Example Problems:**
    - [Ninjas Training](../topics/ninjas-training.md)
    - [Grid Unique Paths](../topics/grid-unique-paths.md)
    - [Unique Paths 2](../topics/unique-paths-2.md)
    - [Minimum Path Sum in Grid](../topics/minimum-path-sum-in-grid.md)
    - [Triangle DP](../topics/triangle-dp.md)
    - [Cherry Pickup II](../topics/cherry-pickup-ii.md)

---

## 3. Subsequence DP
**Description:** Problems where you pick or skip elements from an array to form a subsequence that satisfies a condition.
- **पहचान (Identification):**
    - "Find a subset with sum K."
    - "Partition array into two subsets."
    - "Knapsack problems" (0/1 or Unbounded).
    - Uses the **Pick/Non-Pick** strategy.
- **Example Problems:**
    - [Subset Sum Equals Target](../topics/subset-sum-equals-target.md)
    - [0-1 Knapsack](../topics/0-1-knapsack.md)
    - [Minimum Coins](../topics/minimum-coins.md)
    - [Target Sum](../topics/target-sum.md)
    - [Rod Cutting Problem](../topics/rod-cutting-problem.md)

---

## 4. DP on Strings
**Description:** Problems involving comparisons or operations on two or more strings.
- **पहचान (Identification):**
    - "Longest Common Subsequence/Substring."
    - "Edit Distance" (Convert string A to B).
    - Palindromic properties within strings.
    - Wildcard or Regex matching.
- **Example Problems:**
    - [Longest Common Subsequence](../topics/longest-common-subsequence.md)
    - [Edit Distance](../topics/edit-distance.md)
    - [Longest Palindromic Subsequence](../topics/longest-palindromic-subsequence.md)
    - [Distinct Subsequences](../topics/distinct-subsequences.md)
    - [Wildcard Matching](../topics/wildcard-matching.md)

---

## 5. Stock DP
**Description:** Problems focused on maximizing profit from buying and selling stocks under specific constraints.
- **पहचान (Identification):**
    - "Maximum profit with K transactions."
    - "Unlimited transactions but cannot hold more than one stock."
    - "Transaction fees" or "Cooldown periods."
- **Example Problems:**
    - [Best Time to Buy and Sell Stock](../topics/best-time-to-buy-and-sell-stock.md)
    - [Buy and Sell Stock II](../topics/buy-and-sell-stock-ii.md)
    - [Buy and Sell Stock with Cooldown](../topics/buy-and-sell-stocks-with-cooldown.md)
    - [Buy and Sell Stock with Transaction Fee](../topics/buy-and-sell-stocks-with-transaction-fee.md)

---

## 6. LIS (Longest Increasing Subsequence) Type
**Description:** Problems centered around finding the longest subsequence that follows an increasing or specific order.
- **पहचान (Identification):**
    - Elements must follow an order: $arr[i] > arr[prev\_index]$.
    - "Largest Divisible Subset."
    - "Longest String Chain."
    - "Bitonic Subsequences."
- **Example Problems:**
    - [Longest Increasing Subsequence](../topics/longest-increasing-subsequence.md)
    - [Largest Divisible Subset](../topics/largest-divisible-subset.md)
    - [Longest String Chain](../topics/longest-string-chain.md)
    - [Longest Bitonic Subsequence](../topics/longest-bitonic-subsequence.md)

---

## 7. Partition DP (MCM Type)
**Description:** Problems where the array is partitioned at various points, and the solution is built from these partitions.
- **पहचान (Identification):**
    - Problems involving optimal "cutting" or "splitting."
    - "Matrix Chain Multiplication."
    - Result depends on the order of operations.
- **Example Problems:**
    - [Matrix Chain Multiplication](../topics/matrix-chain-multiplication.md)
    - [Minimum Cost to Cut the Stick](../topics/minimum-cost-to-cut-the-stick.md)
    - [Burst Balloons](../topics/burst-balloons.md)
    - [Palindrome Partitioning II](../topics/palindrome-partitioning-ii.md)

---

## 8. DP on Rectangles
**Description:** Problems dealing with subgrids or rectangles within a larger matrix.
- **पहचान (Identification):**
    - "Maximum area rectangle with all 1s."
    - "Count square submatrices."
- **Example Problems:**
    - [Maximum Rectangle Area with all 1s](../topics/maximum-rectangle-area-with-all-1s.md)
    - [Count Square Submatrices with all Ones](../topics/count-square-submatrices-with-all-ones.md)

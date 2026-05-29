# 3 Sum

## Problem Summary
Given an integer array `nums`, return all the triplets `[nums[i], nums[j], nums[k]]` such that `i != j`, `i != k`, and `j != k`, and `nums[i] + nums[j] + nums[k] == 0`. The solution set must not contain duplicate triplets.

## Intuition
To find three numbers that sum to zero, we can fix one number and find the other two. The challenge is handling duplicate triplets without excessive overhead. Sorting the array helps in both using the two-pointer technique and skipping duplicate elements easily.

## Approach

### Brute Force
Use three nested loops to find all triplets. Use a Set of sorted triplets to avoid duplicates.
- **Time Complexity:** $O(N^3 \log(\text{unique triplets}))$
- **Space Complexity:** $O(2 \times \text{number of triplets})$ (to store triplets in a Set).

### Better (Hashing)
Fix two pointers `i` and `j`, and search for `-(nums[i] + nums[j])` in a Hash Set that stores elements between `i` and `j`.
1.  Use a Set to store unique triplets.
2.  Iterate `i` from 0 to `n-1`.
3.  Inside, iterate `j` from `i+1` to `n-1`.
4.  Look for `target = -(nums[i] + nums[j])` in a hash set.
- **Time Complexity:** $O(N^2 \log M)$ (where $M$ is size of set).
- **Space Complexity:** $O(N) + O(\text{triplets})$.

### Optimal (Two Pointers)
1.  **Sort** the array.
2.  Iterate `i` from 0 to `n-1`.
    - If `i > 0` and `nums[i] == nums[i-1]`, **skip** (to avoid duplicate triplets).
    - Initialize two pointers: `j = i + 1` and `k = n - 1`.
    - While `j < k`:
        - `sum = nums[i] + nums[j] + nums[k]`.
        - If `sum < 0`: `j++`.
        - Else if `sum > 0`: `k--`.
        - Else:
            - Found a triplet! Add `[nums[i], nums[j], nums[k]]` to result.
            - `j++`, `k--`.
            - While `j < k` and `nums[j] == nums[j-1]`, `j++` (Skip duplicates).
            - While `j < k` and `nums[k] == nums[k+1]`, `k--` (Skip duplicates).

## Complexity
- **Time Complexity:** $O(N^2)$ (Sorting takes $O(N \log N)$ and nested loops take $O(N^2)$).
- **Space Complexity:** $O(1)$ or $O(N)$ depending on the sorting algorithm implementation (ignoring the space for the output).

## Pattern
Two Pointers (with Sorting).

## Common Mistakes
- **Not Skipping Duplicates:** Forgetting to skip the same values for `i`, `j`, and `k` leads to duplicate triplets in the result.
- **Sorting:** Trying to use two pointers on an unsorted array.

## Related Problems
- 2 Sum
- 4 Sum
- 3 Sum Closest

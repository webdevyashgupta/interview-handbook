# 4 Sum

## Problem Summary
Given an array of integers `nums` and a target integer `target`, find all unique quadruplets `[nums[a], nums[b], nums[c], nums[d]]` such that:
- `0 <= a, b, c, d < n`
- `a, b, c, and d` are distinct.
- `nums[a] + nums[b] + nums[c] + nums[d] == target`

## Intuition
The problem is an extension of 2-Sum and 3-Sum. The goal is to find four elements that sum up to a target. To avoid duplicates and optimize the search, sorting and using multiple pointers or hashing are common strategies.

## Approach

### 1. Brute Force
Use four nested loops to check every possible combination of four elements.
- **Steps:**
    1. Use four loops with indices `i`, `j`, `k`, and `l`.
    2. For each combination, check if `nums[i] + nums[j] + nums[k] + nums[l] == target`.
    3. To ensure uniqueness, sort the quadruplet and store it in a Set.
    4. Convert the set to a list and return.
- **Time Complexity:** $O(N^4)$ where $N$ is the size of the array.
- **Space Complexity:** $O(2 \times \text{number of quadruplets})$ for storing unique results.

### 2. Better Approach (Hashing)
Reduce one loop by using a hash set to store elements between the third and fourth index.
- **Steps:**
    1. Use three nested loops with indices `i`, `j`, and `k`.
    2. For each triplet, calculate the required fourth element: `fourth = target - (nums[i] + nums[j] + nums[k])`.
    3. Use a hash set to store elements seen between index `j` and `k`.
    4. If `fourth` exists in the set, a valid quadruplet is found.
    5. Use a set to handle unique quadruplets.
- **Time Complexity:** $O(N^3 \times \log(\text{size of set}))$.
- **Space Complexity:** $O(N)$ for the hash set + $O(2 \times \text{number of quadruplets})$.

### 3. Optimal Approach (Two Pointers)
Sort the array and use two fixed pointers with two moving pointers.
- **Steps:**
    1. Sort the array.
    2. Fix the first element using loop `i`. Skip duplicates for `i`.
    3. Fix the second element using loop `j` (starting from `i+1`). Skip duplicates for `j`.
    4. Use two pointers, `low = j + 1` and `high = n - 1`, to find the remaining two elements.
    5. If `sum == target`, add to result and move both `low` and `high` pointers, skipping duplicates.
    6. If `sum < target`, increment `low`.
    7. If `sum > target`, decrement `high`.
- **Space Complexity:** $O(1)$ (excluding the space for the result).

## Implementation

```python
def fourSum(nums, target):
    """
    Finds all unique quadruplets that sum up to target.
    Optimal Approach: Sorting + Two Pointers (Fixed i and j, moving low and high)
    Time Complexity: O(N^3), Space Complexity: O(1) (ignoring output)
    """
    nums.sort()
    n = len(nums)
    result = []
    
    for i in range(n):
        # Skip duplicates for i
        if i > 0 and nums[i] == nums[i-1]:
            continue
        for j in range(i + 1, n):
            # Skip duplicates for j
            if j > i + 1 and nums[j] == nums[j-1]:
                continue
            
            # Two pointers: low and high
            low = j + 1
            high = n - 1
            while low < high:
                current_sum = nums[i] + nums[j] + nums[low] + nums[high]
                if current_sum == target:
                    result.append([nums[i], nums[j], nums[low], nums[high]])
                    low += 1
                    high -= 1
                    # Skip duplicates for low and high
                    while low < high and nums[low] == nums[low-1]:
                        low += 1
                    while low < high and nums[high] == nums[high+1]:
                        high -= 1
                elif current_sum < target:
                    low += 1
                else:
                    high -= 1
                    
    return result
```

## Complexity
- **Time Complexity:** $O(N^3)$ for the optimal two-pointer approach.
- **Space Complexity:** $O(1)$ or $O(\text{number of quadruplets})$ depending on whether the output list is counted.

## Pattern
- **Two Pointers**: Used after sorting to reduce $O(N^2)$ search to $O(N)$.
- **Multi-loop Optimization**: Fixing $k-2$ pointers and using two pointers for the remaining two.

## Common Mistakes
- **Not skipping duplicates**: Forgetting to skip same values for `i`, `j`, `low`, and `high` will lead to duplicate quadruplets in the result.
- **Integer Overflow**: When summing four integers, the result might exceed the 32-bit integer range. Use `long long` in C++ or appropriate types.

## Related Problems
- 2 Sum
- 3 Sum
- 3 Sum Closest
- 4 Sum II (Count quadruplets from four different arrays)

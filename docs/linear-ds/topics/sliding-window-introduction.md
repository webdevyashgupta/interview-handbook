# Introduction to Sliding Window and Two Pointers

## Summary
Sliding Window and Two Pointers are powerful techniques used to solve problems involving arrays or strings, particularly those asking for a contiguous subarray or substring that satisfies a certain condition.

## Core Concepts
The technique involves using two pointers, typically `L` (left) and `R` (right), to define a "window" over the data structure.

### 4 Main Patterns

#### 1. Constant Window
- **Scenario**: The window size $K$ is fixed.
- **Approach**: 
    1. Calculate the result for the first window of size $K$.
    2. Move the window by one position: add the new element at `R` and remove the element at `L`.
    3. Keep track of the maximum/minimum result found.

#### 2. Longest Subarray/Substring with Condition
- **Scenario**: Find the maximum length of a subarray that satisfies a condition (e.g., `sum <= K`).
- **Approach**:
    1. Expand the window by moving `R`.
    2. If the condition is violated, shrink the window from the left by moving `L`.
    - **Optimization ($O(N)$)**: Instead of shrinking with a `while` loop until valid, use an `if` condition to shrink just enough to maintain the current maximum size. This keeps the window size "maximized" and avoids unnecessary shrinking if we only care about the maximum length.

#### 3. Number of Subarrays with Condition
- **Scenario**: Count all subarrays satisfying a condition (e.g., `sum == K`).
- **Approach**: Often solved by calculating `count(condition <= K) - count(condition <= K-1)`. The `count(condition <= K)` part is solved using the standard sliding window pattern.

#### 4. Minimum Window / Shortest Subarray
- **Scenario**: Find the shortest subarray that satisfies a condition (e.g., contains all characters of another string).
- **Approach**:
    1. Expand `R` until the condition is met (window becomes "valid").
    2. Once valid, shrink `L` as much as possible while keeping the window valid to find the minimum size.

## Template (Longest Subarray)
```python
L = 0
R = 0
max_len = 0
current_state = 0 # sum, count, etc.

while R < len(nums):
    # 1. Expand
    current_state += nums[R]
    
    # 2. Shrink if invalid (Better: while loop | Optimal: if condition)
    if current_state > K:
        current_state -= nums[L]
        L += 1
    
    # 3. Update Answer (only if valid)
    if current_state <= K:
        max_len = max(max_len, R - L + 1)
        
    R += 1
```

## Complexity
- **Time**: $O(N)$ or $O(2N)$. Even with a nested loop, each pointer `L` and `R` only moves from $0$ to $N$ once.
- **Space**: $O(1)$ usually, or $O(K)$ if using a hash map to store frequencies.

## Common Mistakes
- **Off-by-one errors**: Be careful with window length calculations (`R - L + 1`).
- **Condition checking**: Ensure you update the "state" (sum, count, map) correctly before and after moving pointers.
- **Shrinking logic**: Knowing when to use a `while` loop (to find all valid windows) vs. an `if` (to find just the longest).

## Related Problems
- Longest Substring Without Repeating Characters
- Max Consecutive Ones III
- Minimum Window Substring

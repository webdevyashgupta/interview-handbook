# Find the Missing and Repeating Number

## Problem Summary
You are given a read-only array of $n$ integers from $1$ to $n$. Each integer appears exactly once except $x$ which appears twice and $y$ which is missing. Return $x$ and $y$.

## Intuition
Since the numbers are in the range $[1, n]$, we can use mathematical identities (sum and sum of squares) or bit manipulation (XOR) to find the two unknowns $x$ and $y$ in linear time and constant space.

## Approach

### 1. Brute Force
Iterate through all numbers from 1 to $n$ and count their frequency in the array.
- **Steps:**
    1. For each `i` from 1 to `n`:
        - Count occurrences of `i` in the array.
        - If count is 2, `repeating = i`.
        - If count is 0, `missing = i`.
    2. Return `[repeating, missing]`.
- **Time Complexity:** $O(N^2)$.
- **Space Complexity:** $O(1)$.

### 2. Better Approach (Hashing)
Use a frequency array (or hash map) to count occurrences.
- **Steps:**
    1. Create a hash array of size `n + 1` initialized to 0.
    2. Traverse the array and increment counts in the hash array.
    3. Traverse the hash array from 1 to `n`:
        - If count is 2, it's the repeating number.
        - If count is 0, it's the missing number.
- **Time Complexity:** $O(N)$.
- **Space Complexity:** $O(N)$.

### 3. Optimal Approach (Mathematical)
Use the sum of first $n$ numbers and sum of their squares.
- **Steps:**
    1. Let $x$ be repeating and $y$ be missing.
    2. Sum difference: $S - S_n = x - y$ (where $S$ is array sum, $S_n = n(n+1)/2$).
    3. Square sum difference: $S^2 - S_n^2 = x^2 - y^2 = (x-y)(x+y)$.
    4. Calculate $x+y = (S^2 - S_n^2) / (x-y)$.
    5. Solve for $x$ and $y$ using $x-y$ and $x+y$.
- **Time Complexity:** $O(N)$.
- **Space Complexity:** $O(1)$.

### 4. Optimal Approach (XOR)
Use bitwise XOR to isolate the two numbers.
- **Steps:**
    1. XOR all array elements and all numbers from 1 to $n$. Result $XR = x \oplus y$.
    2. Find the rightmost set bit in $XR$.
    3. Partition the array elements and numbers from 1 to $n$ into two groups based on this bit.
    4. XORing each group separately will yield $x$ and $y$.
    5. Verify which is $x$ and which is $y$ by a quick scan.
- **Time Complexity:** $O(N)$.
- **Space Complexity:** $O(1)$.

## Complexity
- **Time Complexity:** $O(N)$ for optimal approaches.
- **Space Complexity:** $O(1)$ for optimal approaches.

## Pattern
- **Mathematical Identities**: Using sum and square sum to solve for two variables.
- **Bit Manipulation (XOR)**: Using the property $A \oplus A = 0$ to find missing/repeating elements.

## Common Mistakes
- **Integer Overflow**: In the mathematical approach, $S^2$ and $S_n^2$ can exceed 64-bit integer limits for large $n$. Use `long long` and careful calculations.
- **XOR grouping**: Forgetting to XOR with both the array elements AND the numbers from 1 to $n$ when finding the two groups.

## Related Problems
- Find the Duplicate Number
- Missing Number
- Single Number
- Single Number III (Finding two unique numbers)

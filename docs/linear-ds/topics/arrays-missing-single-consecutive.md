# Arrays: Missing Number, Max Consecutive Ones & Single Number

## 🧩 Problem Summary
1. **Find Missing Number**: Given an array containing $n-1$ distinct numbers in the range $[1, n]$, find the one number that is missing.
2. **Max Consecutive Ones**: Given a binary array, find the maximum number of consecutive 1s.
3. **Single Number**: Given a non-empty array of integers where every element appears twice except for one, find that single one.

- **Input**: Array of integers.
- **Output**: The missing number, count of 1s, or the unique element.
- **Constraints**: For Missing Number, numbers are in range $[1, n]$. For Single Number, all but one appear exactly twice.

## 💡 Intuition
- **Missing Number**: We can use mathematical properties (sum of first $n$ natural numbers) or bit manipulation (XOR) to isolate the missing value.
- **Max Consecutive Ones**: A simple linear scan with a counter that resets when a 0 is encountered.
- **Single Number**: XORing a number with itself results in 0 ($X \oplus X = 0$). XORing all elements together will "cancel out" the pairs, leaving only the single number.

## 🔁 Approach

### 1. Brute Force
- **Missing Number**: For each number $i$ from $1$ to $n$, perform a linear search in the array to see if it exists. Time: $O(N^2)$.
- **Single Number**: For each element, count its occurrences in the array using another loop. If count is 1, return it. Time: $O(N^2)$.

### 2. Better Approach
- **Missing Number (Hashing)**: Create a hash array (frequency array) of size $n+1$. Iterate through the array and mark present numbers. The index with 0 frequency is the answer. Time: $O(N)$, Space: $O(N)$.
- **Single Number (Hashing)**: Use a Hash Map to store frequencies of all elements. Iterate through the map to find the key with frequency 1. Time: $O(N)$, Space: $O(N)$.

### 3. Optimal Approach
- **Missing Number (XOR)**: XOR all numbers from $1$ to $n$ (let this be $X1$) and XOR all elements in the array (let this be $X2$). The missing number is $X1 \oplus X2$.
- **Max Consecutive Ones**: Initialize `cnt = 0` and `maxi = 0`. Iterate through the array: if `arr[i] == 1`, `cnt += 1` and `maxi = max(maxi, cnt)`. Else, `cnt = 0`.
- **Single Number (XOR)**: XOR all elements in the array. The result is the single number.

```python
# Missing Number (XOR Optimal)
def missing_number(arr, n):
    xor1 = 0
    xor2 = 0
    for i in range(n - 1):
        xor2 ^= arr[i]
        xor1 ^= (i + 1)
    xor1 ^= n
    return xor1 ^ xor2

# Max Consecutive Ones
def find_max_consecutive_ones(nums):
    maxi = 0
    cnt = 0
    for x in nums:
        if x == 1:
            cnt += 1
            maxi = max(maxi, cnt)
        else:
            cnt = 0
    return maxi

# Single Number (XOR Optimal)
def single_number(nums):
    xorr = 0
    for x in nums:
        xorr ^= x
    return xorr
```

## Implementation

```python
def missing_number_optimal(arr, n):
    """
    Finds the missing number using XOR.
    Time: O(N), Space: O(1)
    """
    xor1 = 0
    xor2 = 0
    for i in range(n - 1):
        xor2 ^= arr[i]
        xor1 ^= (i + 1)
    xor1 ^= n
    return xor1 ^ xor2

def max_consecutive_ones(nums):
    """
    Finds maximum consecutive ones in a binary array.
    Time: O(N), Space: O(1)
    """
    maxi = 0
    cnt = 0
    for x in nums:
        if x == 1:
            cnt += 1
            maxi = max(maxi, cnt)
        else:
            cnt = 0
    return maxi

def single_number_optimal(nums):
    """
    Finds the element that appears only once using XOR.
    Time: O(N), Space: O(1)
    """
    xorr = 0
    for x in nums:
        xorr ^= x
    return xorr
```

## ⏱️ Complexity
- **Time**: All optimal solutions are $O(N)$ (single pass).
- **Space**: All optimal solutions are $O(1)$ (no extra data structures used).

## 📌 Pattern
- **Bit Manipulation (XOR)**: Used to cancel out duplicates or identify missing parts.
- **Linear Scan (Greedy/Counter)**: Used for consecutive counting.

## ⚠️ Common Mistakes
- **Sum Overflow**: In the "Missing Number" sum approach ($n(n+1)/2$), the sum might overflow if $n$ is very large (e.g., $10^5$). XOR approach is safer.
- **Single Number**: Using hashing when $O(1)$ space is requested.
- **Max Consecutive Ones**: Forgetting to update `maxi` inside the `if` block or forgetting to reset `cnt` on 0.

## 🔗 Related Problems
- Find the duplicate number (Floyd's Cycle)
- Missing and Repeating numbers
- XOR queries on a subarray

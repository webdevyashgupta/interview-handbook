# Majority Element I

## Problem Summary
Given an array of $n$ integers, find the element that appears more than $\lfloor n/2 \rfloor$ times. Such an element is called the majority element. It is guaranteed (or usually assumed) that the majority element always exists, but if not, return -1.

## Intuition
- **Brute Force**: If we count the frequency of each element by scanning the rest of the array for every element, we can find the one that exceeds $n/2$.
- **Better**: To avoid re-scanning, we can store the counts in a Hash Map.
- **Optimal**: The core idea is that if we "cancel out" each occurrence of the majority element with an occurrence of a different element, the majority element will still be left over because it appears more than half the time. This is the basis of **Moore's Voting Algorithm**.

## Approach

### Brute Force
- **Method**: Use two nested loops. The outer loop picks an element, and the inner loop counts its occurrences.
- **Time Complexity**: $O(N^2)$
- **Space Complexity**: $O(1)$

### Better Approach (Hashing)
- **Method**:
    1. Create a Hash Map to store the frequency of each element.
    2. Iterate through the array once and update frequencies.
    3. Iterate through the map to find the element with a value $> n/2$.
- **Time Complexity**: $O(N \log N)$ (for ordered map) or $O(N)$ (for unordered map on average).
- **Space Complexity**: $O(N)$ (to store elements in the map).

### Optimal Approach (Moore's Voting Algorithm)
- **Method**:
    1. Initialize `count = 0` and `element = NULL`.
    2. Traverse the array:
        - If `count == 0`: set `element = current` and `count = 1`.
        - Else if `current == element`: increment `count`.
        - Else: decrement `count`.
    3. **Verification Step**: After the loop, the `element` is a potential candidate. If the problem doesn't guarantee a majority exists, iterate through the array one more time to count the occurrences of this candidate and verify if it's actually $> n/2$.
- **Time Complexity**: $O(N)$ for the first pass + $O(N)$ for the verification pass = $O(2N)$.
- **Space Complexity**: $O(1)$

## Implementation

```python
def majorityElement(nums: list[int]) -> int:
    candidate = None
    count = 0
    
    # Boyer-Moore Voting Algorithm
    for num in nums:
        if count == 0:
            candidate = num
            count = 1
        elif num == candidate:
            count += 1
        else:
            count -= 1
            
    # Optional verification if not guaranteed to exist
    # count = sum(1 for num in nums if num == candidate)
    # return candidate if count > len(nums) // 2 else -1
    
    return candidate
```

## Complexity
- **Time**: $O(N)$ - Two linear passes at most.
- **Space**: $O(1)$ - Only a few variables used.

## Pattern
- **Voting Algorithm**: Using a counter to track a candidate and canceling it out with non-matching elements.

## Common Mistakes
- **No Verification**: Forgetting to verify the candidate if the majority element is not guaranteed to exist.
- **Initialization**: Starting with `count = 1` without setting the first element correctly.

## Related Problems
- Majority Element II ($n/3$ times).
- Finding the mode in an array.

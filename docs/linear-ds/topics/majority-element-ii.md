# Majority Element II (n/3 times)

## Problem Summary
Given an integer array of size `n`, find all elements that appear more than $\lfloor n/3 \rfloor$ times.

## Intuition
Mathematically, there can be at most **two** elements that appear more than $n/3$ times in an array. This is an extension of the Boyer-Moore Voting Algorithm used for the majority element ($> n/2$). We maintain two candidates and two counters to track potential elements.

## Approach

### Brute Force
Use two nested loops to count the frequency of each element and check if it exceeds $n/3$. Use a result list to avoid duplicates.
- **Time Complexity:** $O(N^2)$
- **Space Complexity:** $O(1)$ (excluding output)

### Better (Hashing)
Use a Hash Map to store frequencies of all elements. Iterate through the map and pick elements with `count > n/3`.
- **Time Complexity:** $O(N)$
- **Space Complexity:** $O(N)$

### Optimal (Extended Boyer-Moore Voting Algorithm)
1.  Initialize `cnt1 = 0, cnt2 = 0, el1 = null, el2 = null`.
2.  Traverse the array:
    - If `cnt1 == 0` and `nums[i] != el2`: set `el1 = nums[i], cnt1 = 1`.
    - Else if `cnt2 == 0` and `nums[i] != el1`: set `el2 = nums[i], cnt2 = 1`.
    - Else if `nums[i] == el1`: increment `cnt1`.
    - Else if `nums[i] == el2`: increment `cnt2`.
    - Else: decrement both `cnt1` and `cnt2`.
3.  **Manual Check:** The above step only provides candidates. Since there might not be elements appearing $> n/3$ times, traverse the array again to count actual occurrences of `el1` and `el2`.
4.  If actual counts $> n/3$, add them to the result list.

## Implementation

```python
def majorityElement(nums: list[int]) -> list[int]:
    if not nums:
        return []
    
    candidate1, candidate2 = None, None
    count1, count2 = 0, 0
    
    # Extended Boyer-Moore Voting Algorithm
    for num in nums:
        if candidate1 is not None and num == candidate1:
            count1 += 1
        elif candidate2 is not None and num == candidate2:
            count2 += 1
        elif count1 == 0:
            candidate1 = num
            count1 = 1
        elif count2 == 0:
            candidate2 = num
            count2 = 1
        else:
            count1 -= 1
            count2 -= 1
            
    # Verification pass
    res = []
    n = len(nums)
    if candidate1 is not None and nums.count(candidate1) > n // 3:
        res.append(candidate1)
    if candidate2 is not None and nums.count(candidate2) > n // 3:
        res.append(candidate2)
        
    return res
```

## Complexity
- **Time Complexity:** $O(N) + O(N) = O(N)$ (Two passes).
- **Space Complexity:** $O(1)$ as we only use a few variables.

## Pattern
Voting Algorithm (Boyer-Moore).

## Common Mistakes
- **Missing the Second Pass:** Unlike the $n/2$ case where a majority element is guaranteed to exist in some problem versions, for $n/3$, the candidates must be verified.
- **Incorrect Initialization:** Ensuring `el1` and `el2` are distinct and handling `cnt` resets correctly.

## Related Problems
- Majority Element I (> n/2)
- Boyer-Moore Voting Algorithm

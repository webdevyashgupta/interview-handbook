# Next Permutation

## Problem Summary
Given an array of integers `nums`, find the next lexicographically greater permutation of its elements. If such an arrangement is not possible (i.e., the array is sorted in descending order), it must be rearranged as the lowest possible order (i.e., sorted in ascending order). The replacement must be in place and use only constant extra memory.

---

## Intuition
A lexicographically larger permutation is one that has a larger prefix match with the original but changes at some point to a larger value. To find the *next* permutation, we want to keep the prefix as long as possible. We look for the first "dip" from the right, which indicates where we can swap a smaller element with a slightly larger one from its right side to create the next sequence.

---

## Approach

### Brute Force
1. **Generate all permutations** using recursion.
2. **Sort** the generated permutations lexicographically.
3. **Linear search** to find the given permutation.
4. **Return the next** permutation in the sorted list. If it's the last one, return the first one.
- **Time Complexity:** $O(N! \cdot N)$ to generate and $O(N! \log N!)$ to sort.
- **Space Complexity:** $O(N! \cdot N)$ to store all permutations.

### Better Approach (C++ STL)
In C++, the `next_permutation` function from the STL can be used directly.
```cpp
next_permutation(nums.begin(), nums.end());
```
- **Time Complexity:** $O(N)$
- **Space Complexity:** $O(1)$

### Optimal Approach (Grah-based Observation)
1. **Find the Breakpoint:** Iterate from the right ($n-2$ to 0) to find the first index `i` such that `nums[i] < nums[i+1]`. This is the "breakpoint".
2. **Handle Edge Case:** If no such index exists (array is sorted descending), reverse the entire array and return (it's the last permutation).
3. **Find the Smallest Greater Element:** If a breakpoint is found at `index`, iterate from the right again to find the first element `nums[i]` that is greater than `nums[index]`.
4. **Swap:** Swap `nums[index]` and `nums[i]`.
5. **Reverse the Right Half:** Reverse the sub-array starting from `index + 1` to the end. Since the elements to the right of `index` were originally in descending order, reversing them makes that portion the smallest possible (ascending).

## Implementation

```python
def nextPermutation(nums: list[int]) -> None:
    n = len(nums)
    i = n - 2
    
    # 1. Find the breakpoint
    while i >= 0 and nums[i] >= nums[i + 1]:
        i -= 1
        
    if i >= 0:
        # 2. Find the smallest element greater than nums[i]
        j = n - 1
        while nums[j] <= nums[i]:
            j -= 1
        # 3. Swap
        nums[i], nums[j] = nums[j], nums[i]
        
    # 4. Reverse the right half
    l, r = i + 1, n - 1
    while l < r:
        nums[l], nums[r] = nums[r], nums[l]
        l += 1
        r -= 1
```

---

## Complexity
- **Time Complexity:** $O(3N) \approx O(N)$. We perform at most three passes over the array (finding the breakpoint, finding the element to swap, and reversing).
- **Space Complexity:** $O(1)$ as we modify the array in place without using extra data structures.

---

## Pattern
- Two Pointers
- Mathematical Observation (Lexicographical order)

---

## Common Mistakes
- **Forgetting the Edge Case:** Not handling the case where the array is already the largest possible permutation (descending order).
- **Incorrect Swap:** Swapping with just any element instead of the *smallest element greater than* the breakpoint value.
- **Not Reversing:** Forgetting to reverse the right side after the swap, which results in a permutation much larger than the immediate "next" one.

---

## Related Problems
- Permutations I & II
- Permutation Sequence
- Previous Permutation

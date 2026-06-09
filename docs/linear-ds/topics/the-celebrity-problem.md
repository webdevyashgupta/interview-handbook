# The Celebrity Problem

## Problem Summary
In a party of $N$ people, a "celebrity" is someone who:
1. Is known by everyone else.
2. Knows no one else.

Given an $N \times N$ adjacency matrix `M` where `M[i][j] = 1` means person $i$ knows person $j$, find the celebrity if one exists. Otherwise, return -1.

## Intuition
A celebrity is a unique person. There can be at most one celebrity (if X is a celebrity, they know no one, so they cannot know Y; if Y is also a celebrity, they must be known by everyone, including X, which contradicts the fact that X knows no one).
The brute force solution involves checking each person's row and column, taking $O(N^2)$ time.
The optimal strategy uses **elimination**. If A knows B, A cannot be the celebrity. If A does not know B, B cannot be the celebrity.

## Approach
### Optimal Elimination (Two Pointers)
1. Initialize two pointers: `top = 0` and `down = N - 1`.
2. Move pointers inward based on relationships:
   - If `M[top][down] == 1` (top knows down): `top` cannot be the celebrity. Increment `top`.
   - Else (top does not know down): `down` cannot be the celebrity. Decrement `down`.
3. After the loop, `top` (or `down`) is the only potential candidate.
4. **Final Verification**:
   - Check if the candidate's row is all 0s (except for the diagonal).
   - Check if the candidate's column is all 1s (except for the diagonal).
   - If both conditions pass, return the candidate.

## Implementation

```python
def celebrity(M, n):
    top = 0
    down = n - 1
    
    # Step 1: Elimination
    while top < down:
        if M[top][down] == 1:
            top += 1
        else:
            down -= 1
            
    candidate = top
    
    # Step 2: Verification
    for i in range(n):
        if i != candidate:
            if M[candidate][i] == 1 or M[i][candidate] == 0:
                return -1
                
    return candidate
```

## Complexity
- **Time Complexity**: $O(N)$. We eliminate one person in each step of the two-pointer phase and then perform a single $O(N)$ verification pass.
- **Space Complexity**: $O(1)$. No extra space is used except for pointers.

## Pattern
- **Elimination Technique**: Narrowing down the search space by discarding elements that definitely don't meet the criteria.

## Common Mistakes
- Forgetting the final verification step. The elimination only finds a *candidate*; we must confirm they are known by everyone and know no one.
- Incorrectly handling the diagonal element during verification (a person doesn't need to "know" themselves in the matrix).

## Related Problems
- Find the Judge (LeetCode 997)
- Find the Winner of an Array Game

# Number of Subarrays with XOR K

## Problem Summary
Given an array of integers `A` and an integer `K`, find the total number of subarrays whose XOR sum is equal to `K`.

## Intuition
This problem is similar to finding the number of subarrays with a given sum. The XOR operation has properties similar to addition:
- $A \oplus A = 0$
- $A \oplus 0 = A$
- If $XR$ is the XOR sum of a prefix and $x$ is the XOR sum of a smaller prefix, then the XOR sum of the subarray between them is $XR \oplus x$.
- If $XR \oplus x = K$, then $x = XR \oplus K$.

## Approach

### 1. Brute Force
Generate all possible subarrays and calculate their XOR sum.
- **Steps:**
    1. Use two nested loops to define the start (`i`) and end (`j`) of the subarray.
    2. Use a third loop to calculate the XOR of elements from `i` to `j`.
    3. If XOR sum equals `K`, increment count.
- **Time Complexity:** $O(N^3)$.
- **Space Complexity:** $O(1)$.

### 2. Better Approach
Optimize the brute force by calculating the XOR sum on the fly in the second loop.
- **Steps:**
    1. Loop `i` from 0 to `N-1`.
    2. Initialize `xorr = 0`.
    3. Loop `j` from `i` to `N-1`.
    4. Update `xorr = xorr ^ A[j]`.
    5. If `xorr == K`, increment count.
- **Time Complexity:** $O(N^2)$.
- **Space Complexity:** $O(1)$.

### 3. Optimal Approach (Hash Map)
Use the prefix XOR property and a hash map to count occurrences of prefix XORs.
- **Steps:**
    1. Maintain a variable `XR` for the prefix XOR sum, initialized to 0.
    2. Use a hash map `m` to store the frequency of prefix XORs seen so far. Initialize `m[0] = 1`.
    3. Iterate through the array:
        - Update `XR = XR ^ A[i]`.
        - Calculate `x = XR ^ K`.
        - Add the count of `x` from the map to the total result: `count += m[x]`.
        - Update the map with the current `XR`: `m[XR]++`.
- **Time Complexity:** $O(N)$ if using an unordered map, or $O(N \log N)$ for an ordered map.
- **Space Complexity:** $O(N)$ to store prefix XOR frequencies.

## Complexity
- **Time Complexity:** $O(N)$ (Average case with Hash Map).
- **Space Complexity:** $O(N)$ for the Hash Map.

## Pattern
- **Prefix XOR**: Using the property that if $PrefixXOR[i] \oplus PrefixXOR[j] = K$, then the subarray between $j+1$ and $i$ has XOR $K$.
- **Hash Map for Counting**: Storing frequencies of previously seen prefix results to find complements in $O(1)$.

## Common Mistakes
- **Forgetting `m[0] = 1`**: If a prefix XOR itself equals `K`, you need to count it. Initializing the map with `0` having a frequency of `1` handles this.
- **Wrong XOR formula**: Forgetting that $x = XR \oplus K$ is derived from $XR \oplus x = K$.

## Related Problems
- Subarray Sum Equals K
- Longest Subarray with Sum K
- Subarrays with Bounded Maximum

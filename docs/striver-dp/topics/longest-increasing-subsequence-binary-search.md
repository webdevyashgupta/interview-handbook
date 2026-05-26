# Longest Increasing Subsequence (Binary Search)

## 🧩 Problem Summary
- **Problem:** Given an array of $N$ integers, find the length of the longest strictly increasing subsequence.
- **Goal:** For large constraints (e.g., $N = 10^5$), the standard $O(N^2)$ DP solution is too slow. This approach finds the length in $O(N \log N)$ time.
- **Note:** This method correctly finds the **length** of the LIS, but the resulting array is **not** necessarily the LIS itself.

## 💡 Intuition
- We maintain a dynamic array `temp` that represents the "best" increasing subsequence found so far.
- "Best" means we want the elements in our subsequence to be as small as possible to allow more room for future elements.
- For every element `x` in the input array:
  1. If `x > last element of temp`, it can extend our current longest increasing subsequence. So, append `x` to `temp`.
  2. If `x` is not larger, it cannot extend the sequence. However, it might be a "better" (smaller) element to have at some position in `temp`. We find the first element in `temp` that is $\ge x$ and replace it with `x`. This doesn't change the length but potentially makes the subsequence more "extensible" for future elements.

## 🔁 Approach

### Binary Search (O(N log N))
- **Explanation:** 
  1. Use a `temp` list to store the elements.
  2. For each element in the array, use `lower_bound` to find the correct position to either append or replace.
  3. The `lower_bound` function uses binary search to find the first element $\ge x$.
- **Code (C++):**
```cpp
#include <bits/stdc++.h>
using namespace std;

int longestIncreasingSubsequence(int arr[], int n) {
    vector<int> temp;
    temp.push_back(arr[0]);
    
    for (int i = 1; i < n; i++) {
        if (arr[i] > temp.back()) {
            // Extend the sequence
            temp.push_back(arr[i]);
        } else {
            // Replace the first element >= arr[i]
            int ind = lower_bound(temp.begin(), temp.end(), arr[i]) - temp.begin();
            temp[ind] = arr[i];
        }
    }
    return temp.size();
}
```

## 🧠 Why Binary Search?
- The `temp` array is always sorted. 
- Finding the replacement index in a sorted array takes $O(\log N)$ using binary search.
- We do this for each of the $N$ elements, leading to a total time complexity of $O(N \log N)$.

## ⏱️ Complexity
- **Time Complexity:** $O(N \log N)$
- **Space Complexity:** $O(N)$ for the `temp` vector.

## 📌 Pattern
- **LIS with Binary Search:** The most efficient way to find the length of LIS for large $N$.

## ⚠️ Common Mistakes
- **Printing the LIS:** The `temp` array at the end is **not** the LIS. For example, if the input is `[1, 7, 8, 4, 5, 6]`, the `temp` array will eventually become `[1, 4, 5, 6]`, which has the correct length (4) but is not the actual LIS (which would be `[1, 4, 5, 6]` or `[1, 7, 8]`). Wait, in this case it is, but for `[10, 20, 30, 5, 15]`, `temp` would be `[5, 15, 30]`, while the LIS is `[10, 20, 30]`. The length is correct, but the elements are mixed.
- **strictly Increasing vs Non-Decreasing:**
  - For **strictly increasing**, use `lower_bound` (first element $\ge x$).
  - For **non-decreasing**, use `upper_bound` (first element $> x$).

# Sum of Subarray Ranges

### Problem Summary
Given an integer array `nums`, return the sum of all subarray ranges of `nums`. The range of a subarray is the difference between the largest and smallest element in the subarray.

### Intuition
The range of a subarray is defined as `max(subarray) - min(subarray)`.
The sum of all ranges can be expressed as:
$\sum (\text{max of subarray} - \text{min of subarray})$
Using the linearity of summation:
$\sum (\text{maxes}) - \sum (\text{mins})$
This reduces the problem to two sub-problems:
1. Finding the **Sum of Subarray Maximums**.
2. Finding the **Sum of Subarray Minimums**.

### Approach
#### Linear Time Approach (Optimal)
1. **Sum of Subarray Minimums**:
   - Use the contribution technique with a monotonic stack to find the sum of minimums of all subarrays in $O(N)$ time.
   - For each `arr[i]`, its contribution is `arr[i] * (number of subarrays where it is the minimum)`.
2. **Sum of Subarray Maximums**:
   - Similar to the minimums, use a monotonic stack to find the sum of maximums of all subarrays in $O(N)$ time.
   - For each `arr[i]`, its contribution is `arr[i] * (number of subarrays where it is the maximum)`.
3. **Subtraction**:
   - Result = `SumOfSubarrayMaximums(nums) - SumOfSubarrayMinimums(nums)`.

#### Brute Force ($O(N^2)$)
- Iterate through all possible subarrays using two nested loops.
- Maintain a running `min` and `max` for each subarray.
- Add `(max - min)` to the total sum.
- This is useful if $N$ is small (up to $10^4$).

### Complexity
- **Time Complexity**: $O(N)$ - We perform two $O(N)$ passes (one for min sum, one for max sum).
- **Space Complexity**: $O(N)$ - To store the monotonic stacks and boundary arrays (PSE, NSE, PGE, NGE).

### Pattern
- **Linearity of Summation**: Breaking down a complex sum into simpler components.
- **Contribution Technique**: Calculating how many times an element is a max or a min.
- **Monotonic Stack**: Finding boundaries for maximums and minimums.

### Common Mistakes
- **Handling Duplicates**: Using strict vs. non-strict inequalities incorrectly on both sides of an element can lead to double-counting or missing subarrays.
- **Complexity Misunderstanding**: Many might stop at $O(N^2)$ because they don't realize the relationship between ranges, maximums, and minimums.

### Related Problems
- Sum of Subarray Minimums
- Next Greater Element
- Previous Smaller Element
- Largest Rectangle in Histogram

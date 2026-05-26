# Maximum Rectangle Area with all 1s (DP on Rectangles)

## 🧩 Problem Summary
- **Problem:** Given an $N \times M$ binary matrix filled with 0s and 1s, find the largest rectangle containing only 1s and return its area.
- **Example:**
```
1 0 1 0 0
1 0 1 1 1
1 1 1 1 1
1 0 0 1 0
```
- The maximum rectangle is in the middle with area $3 \times 2 = 6$ (or $2 \times 3$).

## 💡 Intuition
- **Histogram Conversion:** This problem can be solved by reducing it to the "Largest Rectangle in Histogram" problem.
- **Row-by-Row Processing:** Imagine each row of the matrix as the ground. For each column, calculate the "height" of 1s standing on that ground.
- If `matrix[i][j] == 1`, the height at `(i, j)` is `height[i-1][j] + 1`.
- If `matrix[i][j] == 0`, the height at `(i, j)` is `0`.
- For every row, we get a new histogram. We calculate the maximum rectangle area for that histogram and keep track of the overall maximum.

## 🔁 Approach

### 1. Largest Rectangle in Histogram (Helper)
- **Explanation:** For a given array of heights, use a monotonic stack to find the maximum area in $O(M)$ time.
- **Code (C++):**
```cpp
int largestRectangleArea(vector<int>& heights) {
    int n = heights.size();
    stack<int> st;
    int maxA = 0;
    for (int i = 0; i <= n; i++) {
        while (!st.empty() && (i == n || heights[st.top()] >= heights[i])) {
            int height = heights[st.top()];
            st.pop();
            int width;
            if (st.empty()) width = i;
            else width = i - st.top() - 1;
            maxA = max(maxA, width * height);
        }
        st.push(i);
    }
    return maxA;
}
```

### 2. Main Algorithm
- **Explanation:** Iterate through each row, update the heights array, and call the histogram helper.
- **Code (C++):**
```cpp
int maximalRectangle(vector<vector<char>>& matrix) {
    if (matrix.empty()) return 0;
    int n = matrix.size();
    int m = matrix[0].size();
    vector<int> heights(m, 0);
    int maxArea = 0;

    for (int i = 0; i < n; i++) {
        for (int j = 0; j < m; j++) {
            if (matrix[i][j] == '1') heights[j]++;
            else heights[j] = 0;
        }
        maxArea = max(maxArea, largestRectangleArea(heights));
    }
    return maxArea;
}
```

## 🧠 DP State Definition
- `heights[j]` stores the number of consecutive 1s in column `j` ending at the current row `i`. This "state" is updated as we move from row `i` to `i+1`.

## ⏱️ Complexity
- **Time Complexity:** $O(N \times M)$ because we process each row $N$ times, and for each row, we do an $O(M)$ histogram calculation.
- **Space Complexity:** $O(M)$ to store the heights of the histogram for the current row.

## 📌 Pattern
- **DP on Rectangles:** Converting 2D grid problems into a series of 1D histogram problems.

## ⚠️ Common Mistakes
- **Resetting Heights:** If `matrix[i][j] == 0`, the height **must** be reset to 0, because a rectangle of 1s cannot continue through a 0.
- **Input Type:** Often the matrix is given as `vector<vector<char>>`, so check `matrix[i][j] == '1'` instead of `== 1`.

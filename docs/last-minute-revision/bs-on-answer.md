# Binary Search on Answer

This section covers optimization problems where we can use Binary Search to find the optimal value in a monotonic search space.

---

## 1. Capacity To Ship Packages

**Difficulty: 🟡 Medium** | **Pattern: BS on Answer**

### Problem Summary
Given an array `weights` and an integer `days`, find the minimum weight capacity of a ship that will result in all the packages being shipped within `days` days. Packages must be shipped in the order given.

### Intuition
The minimum capacity must be at least the maximum single weight (otherwise that package can't be shipped). The maximum capacity needed is the sum of all weights (shipping everything in 1 day). 
As capacity increases, the number of days needed decreases (monotonic). This makes it perfect for Binary Search.

### Implementation

```python
def shipWithinDays(weights: list[int], days: int) -> int:
    def can_ship(capacity):
        d = 1
        current_load = 0
        for w in weights:
            if current_load + w > capacity:
                d += 1
                current_load = 0
            current_load += w
        return d <= days

    low = max(weights)
    high = sum(weights)
    ans = high

    while low <= high:
        mid = (low + high) // 2
        if can_ship(mid):
            ans = mid
            high = mid - 1
        else:
            low = mid + 1
    return ans
```

---

## 2. Aggressive Cows

**Difficulty: 🔴 Hard** | **Pattern: BS on Answer**

### Problem Summary
Given an array `stalls` (positions) and an integer `k` (cows), place the cows in the stalls such that the minimum distance between any two of them is as large as possible. Return the largest minimum distance.

### Intuition
Search space: `[1, max(stalls) - min(stalls)]`.
If we can place cows with a minimum distance `d`, we might be able to place them with distance `d-1`, but we want the **maximum** `d`.

### Implementation

```python
def solve(stalls, k):
    stalls.sort()
    
    def can_place(dist):
        count = 1
        last_pos = stalls[0]
        for i in range(1, len(stalls)):
            if stalls[i] - last_pos >= dist:
                count += 1
                last_pos = stalls[i]
        return count >= k

    low = 1
    high = stalls[-1] - stalls[0]
    ans = 1
    
    while low <= high:
        mid = (low + high) // 2
        if can_place(mid):
            ans = mid
            low = mid + 1
        else:
            high = mid - 1
    return ans
```

---

## 3. Book Allocation

**Difficulty: 🔴 Hard** | **Pattern: BS on Answer**

### Problem Summary
Given an array `pages` where `pages[i]` is the number of pages in the $i$-th book and `m` students. Allocate books such that the maximum number of pages assigned to a student is minimized. One student can read contiguous books.

### Intuition
Search space: `[max(pages), sum(pages)]`. 
If we can allocate books such that no student reads more than `X` pages, we try a smaller `X`.

### Implementation

```python
def allocateBooks(pages, n, m):
    if m > n: return -1
    
    def count_students(max_pages):
        students = 1
        pages_student = 0
        for p in pages:
            if pages_student + p <= max_pages:
                pages_student += p
            else:
                students += 1
                pages_student = p
        return students

    low = max(pages)
    high = sum(pages)
    ans = low
    
    while low <= high:
        mid = (low + high) // 2
        if count_students(mid) <= m:
            ans = mid
            high = mid - 1
        else:
            low = mid + 1
    return ans
```

---

## 4. N-th Root of an Integer

**Difficulty: 🟡 Medium** | **Pattern: BS on Value**

### Problem Summary
Find the $n$-th root of an integer $m$. Return the integer part or -1 if it's not a perfect root.

### Implementation

```python
def nthRoot(n, m):
    low = 1
    high = m
    
    while low <= high:
        mid = (low + high) // 2
        val = pow(mid, n)
        if val == m:
            return mid
        elif val < m:
            low = mid + 1
        else:
            high = mid - 1
    return -1
```

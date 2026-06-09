# String Mastery

This section covers advanced string matching and manipulation algorithms.

---

## 1. Repeated String Match

**Difficulty: 🟡 Medium** | **Pattern: String Matching**

### Problem Summary
Given two strings `a` and `b`, return the minimum number of times `a` has to be repeated such that `b` is a substring of it. If it's impossible, return -1.

### Intuition
If `b` is a substring of `a` repeated `k` times, then `k` must be at least `ceil(len(b) / len(a))`.
We only need to check two cases:
1.  `a` repeated `k` times.
2.  `a` repeated `k + 1` times (to handle cases where `b` starts at the end of one `a` and ends at the beginning of another).

### Implementation

```python
import math

def repeatedStringMatch(a: str, b: str) -> int:
    # Minimum repetitions to reach length of b
    k = math.ceil(len(b) / len(a))
    
    # Case 1: k repetitions
    s = a * k
    if b in s:
        return k
    
    # Case 2: k + 1 repetitions
    s += a
    if b in s:
        return k + 1
        
    return -1
```

---

## 2. KMP Algorithm (LPS Array)

**Difficulty: 🟡 Medium** | **Pattern: Pattern Matching**

*Reference for questions like "Find Index of First Occurrence".*

### LPS Array Implementation

```python
def computeLPS(pattern):
    lps = [0] * len(pattern)
    length = 0
    i = 1
    while i < len(pattern):
        if pattern[i] == pattern[length]:
            length += 1
            lps[i] = length
            i += 1
        else:
            if length != 0:
                length = lps[length - 1]
            else:
                lps[i] = 0
                i += 1
    return lps
```

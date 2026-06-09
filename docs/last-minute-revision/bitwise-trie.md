# Bitwise Trie & XOR Mastery

This section covers advanced Trie applications where numbers are stored bit-by-bit to solve XOR-related optimization problems.

---

## 1. Maximum XOR of Two Numbers in an Array

**Difficulty: 🟡 Medium** | **Pattern: Bitwise Trie**

### Problem Summary
Given an integer array `nums`, return the maximum result of `nums[i] XOR nums[j]`.

### Intuition
To maximize XOR, we want the most significant bits to be `1`. 
1. Insert all numbers into a Trie bit by bit (from 31 down to 0).
2. For each number, traverse the Trie. At each bit, try to go to the path that has the **opposite** bit (e.g., if current bit is `1`, try to find a `0` in the Trie).
3. This "greedy" approach at each bit ensures the maximum possible XOR.

### Implementation

```python
class TrieNode:
    def __init__(self):
        self.links = [None] * 2 # 0 and 1

class Trie:
    def __init__(self):
        self.root = TrieNode()
    
    def insert(self, num):
        curr = self.root
        for i in range(31, -1, -1):
            bit = (num >> i) & 1
            if not curr.links[bit]:
                curr.links[bit] = TrieNode()
            curr = curr.links[bit]
            
    def get_max(self, num):
        curr = self.root
        max_xor = 0
        for i in range(31, -1, -1):
            bit = (num >> i) & 1
            # Try to pick the opposite bit
            if curr.links[1 - bit]:
                max_xor |= (1 << i)
                curr = curr.links[1 - bit]
            else:
                curr = curr.links[bit]
        return max_xor

def findMaximumXOR(nums: list[int]) -> int:
    trie = Trie()
    for n in nums: trie.insert(n)
    
    res = 0
    for n in nums:
        res = max(res, trie.get_max(n))
    return res
```

---

## 2. Maximum XOR With an Element from Array

**Difficulty: 🔴 Hard** | **Pattern: Offline Queries + Bitwise Trie**

### Problem Summary
Given an array `nums` and `queries` where `queries[i] = [xi, mi]`. Find the maximum XOR of `xi` with any number in `nums` that is $\le mi$.

### Intuition
This is the same as the previous problem, but with a constraint ($n \le mi$).
If we sort both `nums` and `queries` (by $mi$), we can process queries **offline**.
As we iterate through the sorted queries, we only insert numbers from `nums` into our Trie that satisfy the current $mi$ constraint.

### Implementation

```python
def maximizeXor(nums, queries):
    nums.sort()
    # Add index to queries to return result in original order
    indexed_queries = sorted(enumerate(queries), key=lambda x: x[1][1])
    
    trie = Trie() # Same Trie class from above
    res = [-1] * len(queries)
    nums_idx = 0
    
    for q_idx, (x, m) in indexed_queries:
        # Insert all valid numbers into Trie
        while nums_idx < len(nums) and nums[nums_idx] <= m:
            trie.insert(nums[nums_idx])
            nums_idx += 1
            
        if nums_idx > 0: # If Trie is not empty
            res[q_idx] = trie.get_max(x)
            
    return res
```

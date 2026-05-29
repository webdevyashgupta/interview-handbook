# Linear Data Structures: Essential Templates (Python)

This document provides reusable, production-ready Python templates for common linear data structure patterns.

---

## 1. Two Pointers (Opposite Direction)
Used for sorted arrays, finding pairs, or reversing elements.

```python
def two_pointer_opposite(arr, target):
    """
    Template for finding a pair or processing from both ends.
    Time: O(N) | Space: O(1)
    """
    left, right = 0, len(arr) - 1
    
    while left < right:
        current_val = arr[left] + arr[right]
        
        if current_val == target:
            # Found target, return or process
            return [left, right]
        elif current_val < target:
            left += 1
        else:
            right -= 1
            
    return [-1, -1]
```

---

## 2. Sliding Window (Variable Size)
General template for finding the longest/shortest subarray satisfying a condition.

```python
def sliding_window_variable(arr, condition_func):
    """
    Template for variable size sliding window.
    Time: O(N) | Space: O(K) (depends on state)
    """
    left = 0
    max_len = 0
    state = {} # e.g., frequency map, sum, etc.
    
    for right in range(len(arr)):
        # 1. Expand: Add current element to window state
        char = arr[right]
        state[char] = state.get(char, 0) + 1
        
        # 2. Shrink: While condition is violated, move left pointer
        while not condition_func(state):
            left_char = arr[left]
            state[left_char] -= 1
            if state[left_char] == 0:
                del state[left_char]
            left += 1
            
        # 3. Update result
        max_len = max(max_len, right - left + 1)
        
    return max_len
```

---

## 3. Prefix Sum + Hashing (Subarray Count)
Template for counting subarrays that sum to `K`.

```python
from collections import defaultdict

def count_subarrays_with_sum_k(nums, k):
    """
    Count number of subarrays where sum(subarray) == k.
    Time: O(N) | Space: O(N)
    """
    prefix_map = defaultdict(int)
    prefix_map[0] = 1 # Base case: empty subarray has sum 0
    
    current_sum = 0
    count = 0
    
    for num in nums:
        current_sum += num
        
        # If (current_sum - k) exists in map, it means there's a subarray
        # ending here that sums to k.
        if (current_sum - k) in prefix_map:
            count += prefix_map[current_sum - k]
            
        prefix_map[current_sum] += 1
        
    return count
```

---

## 4. Monotonic Stack (Next Greater Element)
General template for finding the nearest greater/smaller element for every index.

```python
def next_greater_element(arr):
    """
    Find the next greater element for each index.
    Time: O(N) | Space: O(N)
    """
    n = len(arr)
    result = [-1] * n
    stack = [] # Stores indices
    
    # Iterate from right to left for NGE to the right
    # OR iterate left to right and process 'stack.pop()'
    for i in range(n):
        while stack and arr[stack[-1]] < arr[i]:
            idx = stack.pop()
            result[idx] = arr[i]
        stack.append(i)
        
    return result
```

---

## 5. LRU Cache (DLL + Map)
Efficient implementation using a Doubly Linked List and Hash Map.

```python
class Node:
    def __init__(self, key=0, value=0):
        self.key = key
        self.value = value
        self.prev = None
        self.next = None

class LRUCache:
    """
    Least Recently Used Cache implementation.
    get() and put() are O(1).
    """
    def __init__(self, capacity: int):
        self.capacity = capacity
        self.cache = {} # key -> Node
        # Dummy head and tail
        self.head = Node()
        self.tail = Node()
        self.head.next = self.tail
        self.tail.prev = self.head

    def _remove(self, node):
        node.prev.next = node.next
        node.next.prev = node.prev

    def _add(self, node):
        # Add to head (most recent)
        node.next = self.head.next
        node.prev = self.head
        self.head.next.prev = node
        self.head.next = node

    def get(self, key: int) -> int:
        if key in self.cache:
            node = self.cache[key]
            self._remove(node)
            self._add(node)
            return node.value
        return -1

    def put(self, key: int, value: int) -> None:
        if key in self.cache:
            self._remove(self.cache[key])
        
        new_node = Node(key, value)
        self.cache[key] = new_node
        self._add(new_node)
        
        if len(self.cache) > self.capacity:
            # Remove LRU (from tail)
            lru = self.tail.prev
            self._remove(lru)
            del self.cache[lru.key]
```

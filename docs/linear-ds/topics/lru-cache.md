# LRU Cache (Least Recently Used)

## Problem Summary
Design a data structure for Least Recently Used (LRU) cache. It should support:
- `get(key)`: Return the value of the key if it exists, otherwise return -1.
- `put(key, value)`: Update the value if the key exists, or insert the key-value pair if it doesn't. If the cache is at capacity, remove the least recently used item before inserting the new one.
Both operations must run in $O(1)$ time.

## Intuition
To achieve $O(1)$ access, we need a **Hash Map**. To maintain the order of usage and achieve $O(1)$ updates/deletions, we need a **Doubly Linked List (DLL)**.
- The Hash Map stores `key` -> `Node address`.
- The DLL stores `(key, value)` nodes. We keep the most recently used (MRU) items at the front (right after the dummy head) and the least recently used (LRU) items at the back (right before the dummy tail).

## Approach
1. **Data Structures**:
   - `class Node`: `key`, `value`, `next`, `prev`.
   - `HashMap<Integer, Node>`: Maps keys to their respective nodes in the DLL.
   - `dummyHead` and `dummyTail`: Pointers to simplify node insertions/deletions.
2. **`get(key)`**:
   - If key not in map, return -1.
   - If key exists:
     - Remove the node from its current position in the DLL.
     - Re-insert it at the front (right after `dummyHead`) to mark it as MRU.
     - Return the value.
3. **`put(key, value)`**:
   - If key exists:
     - Update the value in the existing node.
     - Move the node to the front (MRU).
   - If key does not exist:
     - If at capacity, delete the node just before `dummyTail` (LRU) from both the DLL and the map.
     - Create a new node.
     - Insert it at the front (MRU).
     - Add the new key-node pair to the map.

## Implementation

```python
class Node:
    def __init__(self, key, value):
        self.key = key
        self.val = value
        self.prev = None
        self.next = None

class LRUCache:
    def __init__(self, capacity: int):
        self.capacity = capacity
        self.cache = {} # key -> Node
        self.head = Node(0, 0)
        self.tail = Node(0, 0)
        self.head.next = self.tail
        self.tail.prev = self.head

    def _remove(self, node):
        node.prev.next = node.next
        node.next.prev = node.prev

    def _add(self, node):
        node.next = self.head.next
        node.prev = self.head
        self.head.next.prev = node
        self.head.next = node

    def get(self, key: int) -> int:
        if key in self.cache:
            node = self.cache[key]
            self._remove(node)
            self._add(node)
            return node.val
        return -1

    def put(self, key: int, value: int) -> None:
        if key in self.cache:
            self._remove(self.cache[key])
        
        node = Node(key, value)
        self.cache[key] = node
        self._add(node)
        
        if len(self.cache) > self.capacity:
            lru = self.tail.prev
            self._remove(lru)
            del self.cache[lru.key]
```

## Complexity
- **Time Complexity**: $O(1)$ for both `get` and `put`. Hash map lookup and DLL pointer updates are constant time operations.
- **Space Complexity**: $O(Capacity)$ for storing the map and the nodes.

## Pattern
- **Hash Map + Doubly Linked List**: The standard combination for $O(1)$ cache implementations.

## Common Mistakes
- Not using dummy head and tail nodes, which makes edge cases (like deleting the only node) much harder to handle.
- Forgetting to remove the key from the Hash Map when a node is evicted from the DLL.
- Not updating the node's position in `get`.

## Related Problems
- LFU Cache
- Design a Hit Counter
- MRU Cache

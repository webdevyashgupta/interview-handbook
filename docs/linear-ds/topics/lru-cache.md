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

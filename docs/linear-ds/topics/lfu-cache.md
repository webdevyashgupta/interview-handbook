# LFU Cache (Least Frequently Used)

## Problem Summary
Design a data structure for Least Frequently Used (LFU) cache. It should support:
- `get(key)`: Return the value if the key exists, otherwise -1.
- `put(key, value)`: Update or insert the key-value pair. If at capacity, remove the **least frequently used** item. If multiple items have the same minimum frequency, remove the **least recently used** one among them.
Both operations must run in $O(1)$ time.

## Intuition
To achieve $O(1)$ for LFU, we need to track both the frequency and the order of usage within each frequency.
- A `keyNodeMap` (Hash Map) to store `key` -> `Node`.
- A `freqListMap` (Hash Map) to store `frequency` -> `Doubly Linked List`. Each DLL will maintain the LRU order for nodes sharing the same frequency.
- A `minFreq` variable to track the current lowest frequency in the cache for efficient eviction.

## Approach
1. **Data Structures**:
   - `class Node`: `key`, `value`, `freq`, `next`, `prev`.
   - `class List`: `head`, `tail`, `size` (a DLL of nodes).
   - `HashMap<Integer, Node> keyNodeMap`: For $O(1)$ access.
   - `HashMap<Integer, List> freqListMap`: Maps frequency to a DLL of nodes with that frequency.
2. **`get(key)`**:
   - If key not in `keyNodeMap`, return -1.
   - Otherwise, update the frequency of the node (internal `updateFreq` logic).
   - Return value.
3. **`put(key, value)`**:
   - If key exists:
     - Update value and `updateFreq`.
   - If key does not exist:
     - If at capacity, find the `List` in `freqListMap` corresponding to `minFreq`. Evict the LRU node (last node before dummy tail) from this list. Remove it from `keyNodeMap`.
     - Create a new node with `freq = 1`.
     - Add it to the frequency 1 list (create list if necessary).
     - Set `minFreq = 1`.
     - Add to `keyNodeMap`.
4. **`updateFreq(node)`**:
   - Remove node from its current frequency list in `freqListMap`.
   - If that list was the `minFreq` list and is now empty, increment `minFreq`.
   - Increment node's `freq`.
   - Add node to the new frequency list in `freqListMap`.

## Implementation

```python
class Node:
    def __init__(self, key, value):
        self.key = key
        self.val = value
        self.freq = 1
        self.prev = None
        self.next = None

class DoublyLinkedList:
    def __init__(self):
        self.head = Node(None, None)
        self.tail = Node(None, None)
        self.head.next = self.tail
        self.tail.prev = self.head
        self.size = 0

    def add_node(self, node):
        node.next = self.head.next
        node.prev = self.head
        self.head.next.prev = node
        self.head.next = node
        self.size += 1

    def remove_node(self, node):
        node.prev.next = node.next
        node.next.prev = node.prev
        self.size -= 1

    def remove_tail(self):
        if self.size == 0:
            return None
        node = self.tail.prev
        self.remove_node(node)
        return node

class LFUCache:
    def __init__(self, capacity: int):
        self.capacity = capacity
        self.size = 0
        self.min_freq = 0
        self.key_map = {} # key -> node
        self.freq_map = {} # freq -> doubly linked list

    def _update_freq(self, node):
        freq = node.freq
        self.freq_map[freq].remove_node(node)
        if self.freq_map[freq].size == 0 and freq == self.min_freq:
            self.min_freq += 1
        
        node.freq += 1
        new_freq = node.freq
        if new_freq not in self.freq_map:
            self.freq_map[new_freq] = DoublyLinkedList()
        self.freq_map[new_freq].add_node(node)

    def get(self, key: int) -> int:
        if key not in self.key_map:
            return -1
        node = self.key_map[key]
        self._update_freq(node)
        return node.val

    def put(self, key: int, value: int) -> None:
        if self.capacity == 0:
            return
        
        if key in self.key_map:
            node = self.key_map[key]
            node.val = value
            self._update_freq(node)
        else:
            if self.size == self.capacity:
                old_list = self.freq_map[self.min_freq]
                removed_node = old_list.remove_tail()
                if removed_node:
                    del self.key_map[removed_node.key]
                    self.size -= 1
            
            new_node = Node(key, value)
            self.key_map[key] = new_node
            if 1 not in self.freq_map:
                self.freq_map[1] = DoublyLinkedList()
            self.freq_map[1].add_node(new_node)
            self.min_freq = 1
            self.size += 1
```

## Complexity
- **Time Complexity**: $O(1)$ for both `get` and `put`.
- **Space Complexity**: $O(Capacity)$.

## Pattern
- **Multiple Hash Maps + Doubly Linked Lists**: Using one map for access and another for managing groups of related data (frequencies).

## Common Mistakes
- Not correctly updating `minFreq` when a node's frequency increases or when a new node is added.
- Not handling the case where a frequency list becomes empty.
- Forgetting to handle the "Least Recently Used" tie-breaking rule within the "Least Frequently Used" requirement.

## Related Problems
- LRU Cache
- All O`one Data Structure
- Design Search Autocomplete System

# Introduction to Stack and Queue

### Problem Summary
Stack and Queue are fundamental linear data structures used to store and manage data. 
- **Stack** follows the **LIFO (Last In, First Out)** principle, where the last element added is the first one to be removed.
- **Queue** follows the **FIFO (First In, First Out)** principle, where the first element added is the first one to be removed.

### Intuition
- **Stack**: Think of a stack of plates. You add a plate to the top and take the top plate off first.
- **Queue**: Think of a line at a ticket counter. The person who arrives first gets served first.

### Approach

#### 1. Stack Implementation
A stack supports the following primary operations:
- `push(val)`: Add an element to the top.
- `pop()`: Remove the top element.
- `top()`: Get the top element without removing it.
- `isEmpty()`: Check if the stack is empty.

**Implementation using Arrays:**
- Use a pointer `top` initialized to -1.
- `push`: Increment `top` and add element at `arr[top]`.
- `pop`: Decrement `top`.

**Implementation using Linked List:**
- Push at the head of the linked list.
- Pop from the head of the linked list.

#### 2. Queue Implementation
A queue supports:
- `push(val)` (or `enqueue`): Add an element to the back.
- `pop()` (or `dequeue`): Remove the element from the front.
- `front()`: Get the front element.
- `isEmpty()`: Check if the queue is empty.

**Implementation using Arrays:**
- Use two pointers `front` and `rear`.
- Alternatively, use a circular array to optimize space.

**Implementation using Linked List:**
- Maintain `head` (front) and `tail` (rear) pointers.
- Push at `tail`, pop from `head`.

### Implementation

```python
class Stack:
    """
    LIFO (Last In, First Out) implementation using a list.
    """
    def __init__(self):
        self.stack = []
        
    def push(self, val):
        self.stack.append(val)
        
    def pop(self):
        if not self.isEmpty():
            return self.stack.pop()
        return None
        
    def top(self):
        if not self.isEmpty():
            return self.stack[-1]
        return None
        
    def isEmpty(self):
        return len(self.stack) == 0

class Queue:
    """
    FIFO (First In, First Out) implementation using a list.
    Note: For O(1) pops in Python, use collections.deque.
    """
    def __init__(self):
        from collections import deque
        self.queue = deque()
        
    def enqueue(self, val):
        self.queue.append(val)
        
    def dequeue(self):
        if not self.isEmpty():
            return self.queue.popleft()
        return None
        
    def front(self):
        if not self.isEmpty():
            return self.queue[0]
        return None
        
    def isEmpty(self):
        return len(self.queue) == 0
```

### Complexity
- **Time Complexity**: All operations (`push`, `pop`, `top`, `front`) are **O(1)**.
- **Space Complexity**: **O(N)** where N is the number of elements stored.

### Pattern
Linear Data Structure - Simple storage with specific access rules (LIFO/FIFO).

### Common Mistakes
- **Stack Overflow**: Trying to push onto a full stack (in array implementation).
- **Stack Underflow**: Trying to pop from an empty stack.
- **Queue Empty Check**: Forgetting to check if the queue is empty before `front()` or `pop()`.
- **Inefficient Queue**: Using an array without circular logic or two pointers can lead to O(N) pops.

### Related Problems
- Implement Stack using Queues
- Implement Queue using Stacks
- Balanced Parentheses
- Min Stack Implementation

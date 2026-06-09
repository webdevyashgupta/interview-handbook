# Advanced Linked List

This section covers complex pointer manipulation used in high-frequency interview questions.

---

## 1. Flatten a Multilevel Linked List

**Difficulty: 🟡 Medium** | **Pattern: Recursion / Stack**

### Problem Summary
You are given a doubly linked list where each node also has a `child` pointer. This child pointer might point to a separate doubly linked list. Flatten the list so that all nodes appear in a single-level doubly linked list.

### Implementation

```python
def flatten(head):
    if not head: return head
    
    dummy = Node(0, None, head, None)
    stack = [head]
    prev = dummy
    
    while stack:
        curr = stack.pop()
        
        # Link prev and curr
        prev.next = curr
        curr.prev = prev
        
        # Push next first, then child (so child is processed first)
        if curr.next:
            stack.append(curr.next)
        if curr.child:
            stack.append(curr.child)
            curr.child = None # Crucial
            
        prev = curr
        
    dummy.next.prev = None
    return dummy.next
```

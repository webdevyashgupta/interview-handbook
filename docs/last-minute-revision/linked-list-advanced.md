# Advanced Linked List & Graphs

This section covers complex pointer manipulation and graph properties used in high-frequency interview questions.

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

---

## 2. Bipartite Graph Check

**Difficulty: 🟡 Medium** | **Pattern: Graph Coloring**

### Problem Summary
A graph is **bipartite** if its vertices can be divided into two independent sets $U$ and $V$ such that every edge $(u, v)$ connects a vertex from $U$ to one from $V$.

### Intuition
Use **two colors** (0 and 1). Start from an uncolored node and perform BFS/DFS. Color neighbors with the opposite color. If you find a neighbor already colored with the **same** color, the graph is not bipartite.

### Implementation

```python
from collections import deque

def isBipartite(graph: list[list[int]]) -> bool:
    n = len(graph)
    colors = [-1] * n # -1: uncolored, 0: color A, 1: color B
    
    for i in range(n):
        if colors[i] == -1:
            # Start BFS for this component
            queue = deque([i])
            colors[i] = 0
            
            while queue:
                u = queue.popleft()
                for v in graph[u]:
                    if colors[v] == -1:
                        colors[v] = 1 - colors[u]
                        queue.append(v)
                    elif colors[v] == colors[u]:
                        return False
    return True
```

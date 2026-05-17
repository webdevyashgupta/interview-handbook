# Graphs — Extra Problems

**Grind 169 Supplement | Not in NeetCode 150**  
[← Back to Index](./index.md)

---

## Problems in this Section

| # | Problem | Difficulty |
|---|---------|------------|
| 1 | [Flood Fill](#1-flood-fill) | 🟢 Easy |
| 2 | [01 Matrix](#2-01-matrix) | 🟡 Medium |
| 3 | [Accounts Merge](#3-accounts-merge) | 🟡 Medium |
| 4 | [Minimum Height Trees](#4-minimum-height-trees) | 🟡 Medium |
| 5 | [Bus Routes](#5-bus-routes) | 🔴 Hard |

---

## 1. Flood Fill

**LeetCode #733 | Difficulty: 🟢 Easy**

### Problem Statement

Given an image (2D array), a starting pixel `(sr, sc)`, and a new color, perform a flood fill — change the starting pixel and all connected pixels of the same original color to the new color.

```
Input:  image = [[1,1,1],[1,1,0],[1,0,1]], sr=1, sc=1, color=2
Output: [[2,2,2],[2,2,0],[2,0,1]]
```

### Intuition

Classic DFS/BFS from the starting pixel. Visit all 4-directional neighbors that have the **same original color** as the start. Change each visited cell to the new color.

Edge case: if the starting pixel already has the new color, return immediately to avoid infinite loops.

### Solution

```python
def floodFill(image: list[list[int]], sr: int, sc: int, color: int) -> list[list[int]]:
    original = image[sr][sc]
    if original == color:
        return image  # already the target color, nothing to do

    rows, cols = len(image), len(image[0])

    def dfs(r, c):
        if r < 0 or r >= rows or c < 0 or c >= cols:
            return
        if image[r][c] != original:
            return
        image[r][c] = color
        dfs(r+1, c); dfs(r-1, c); dfs(r, c+1); dfs(r, c-1)

    dfs(sr, sc)
    return image
```

**Time:** O(m·n) | **Space:** O(m·n) recursion stack

### Interview Traps

**Trap 1 — Not handling the same-color edge case**
If `original == color`, the DFS would run forever — every cell stays the "original" color and keeps getting visited.  
✅ Early return if `original == color`.

**Trap 2 — Using a visited set unnecessarily**
Since we change the color in-place, revisiting is naturally prevented — a cell changed to `color` no longer matches `original`.

**Trap 3 — 8-directional vs 4-directional**
Flood fill is typically 4-directional (up/down/left/right). Clarify with the interviewer if diagonals are included.

**Trap 4 — "How is this different from Number of Islands?"**
Number of Islands counts components. Flood Fill changes all cells in one component. Same DFS/BFS pattern, different output.

---

## 2. 01 Matrix

**LeetCode #542 | Difficulty: 🟡 Medium**

### Problem Statement

Given an `m x n` binary matrix `mat`, return the distance of the nearest `0` for each cell.

```
Input:  mat = [[0,0,0],[0,1,0],[1,1,1]]
Output: [[0,0,0],[0,1,0],[1,2,1]]
```

### Intuition

**Multi-source BFS** from all `0` cells simultaneously (same pattern as Rotting Oranges). Initialize the queue with all zeros at distance 0. BFS outward, filling in distances for `1` cells. The first time a `1` cell is reached, its distance is the shortest.

**Do NOT** run BFS from each `1` cell individually — that's O((m·n)²).

### Solution

```python
from collections import deque

def updateMatrix(mat: list[list[int]]) -> list[list[int]]:
    rows, cols = len(mat), len(mat[0])
    dist = [[float('inf')] * cols for _ in range(rows)]
    queue = deque()

    # Initialize all 0s with distance 0
    for r in range(rows):
        for c in range(cols):
            if mat[r][c] == 0:
                dist[r][c] = 0
                queue.append((r, c))

    # Multi-source BFS
    while queue:
        r, c = queue.popleft()
        for dr, dc in [(0,1),(0,-1),(1,0),(-1,0)]:
            nr, nc = r + dr, c + dc
            if 0 <= nr < rows and 0 <= nc < cols:
                if dist[r][c] + 1 < dist[nr][nc]:
                    dist[nr][nc] = dist[r][c] + 1
                    queue.append((nr, nc))

    return dist
```

**Time:** O(m·n) | **Space:** O(m·n)

### Interview Traps

**Trap 1 — BFS from each 1 cell separately**
O((m·n)²) — too slow. Multi-source BFS from all zeros is O(m·n).

**Trap 2 — DFS instead of BFS**
DFS doesn't guarantee shortest distance. BFS is mandatory for shortest path on unweighted grids.

**Trap 3 — DP approach (valid alternative)**
Two-pass DP: top-left to bottom-right, then bottom-right to top-left. Each pass propagates distances in one direction.
```python
# Pass 1: top-left
for r in range(rows):
    for c in range(cols):
        if mat[r][c] != 0:
            top  = dist[r-1][c] if r > 0 else INF
            left = dist[r][c-1] if c > 0 else INF
            dist[r][c] = min(top, left) + 1
# Pass 2: bottom-right (symmetric)
```

**Trap 4 — Not initializing 1-cells to infinity**
Cells with value 1 start at infinity — only 0-cells start at 0.

---

## 3. Accounts Merge

**LeetCode #721 | Difficulty: 🟡 Medium**

### Problem Statement

Given a list of accounts where each account is `[name, email1, email2, ...]`, merge accounts that share at least one email. Return merged accounts sorted.

```
Input:  [["John","johnsmith@mail.com","john_newyork@mail.com"],
         ["John","johnsmith@mail.com","john00@mail.com"],
         ["Mary","mary@mail.com"]]
Output: [["John","john00@mail.com","john_newyork@mail.com","johnsmith@mail.com"],
         ["Mary","mary@mail.com"]]
```

### Intuition

Use **Union-Find**: treat each email as a node. For each account, union all emails in that account together (using the first email as the root). Also map each email to its account's name.

After all unions, group emails by their root. Sort each group and prepend the name.

### Solution

```python
from collections import defaultdict

def accountsMerge(accounts: list[list[str]]) -> list[list[str]]:
    parent = {}

    def find(x):
        if parent.setdefault(x, x) != x:
            parent[x] = find(parent[x])
        return parent[x]

    def union(x, y):
        parent[find(x)] = find(y)

    email_to_name = {}

    for account in accounts:
        name = account[0]
        for email in account[1:]:
            email_to_name[email] = name
            union(account[1], email)  # union all with first email

    # Group emails by root
    groups = defaultdict(list)
    for email in email_to_name:
        groups[find(email)].append(email)

    return [[email_to_name[root]] + sorted(emails)
            for root, emails in groups.items()]
```

**Time:** O(n · k · α(n)) where k = emails per account | **Space:** O(n · k)

### Interview Traps

**Trap 1 — Graph DFS/BFS approach**
Build an adjacency list of emails (edges between emails in the same account), then DFS to find connected components. Valid but more code than Union-Find.

**Trap 2 — Not mapping email to name**
After merging, you need to know which name each group belongs to. Map every email to its account name.

**Trap 3 — Emails in different accounts with same name are different people**
Don't merge by name — only merge by shared emails. Two "John" accounts are different unless they share an email.

**Trap 4 — Sorting the final output**
Emails within each account must be sorted. The account order doesn't matter.

---

## 4. Minimum Height Trees

**LeetCode #310 | Difficulty: 🟡 Medium**

### Problem Statement

Given a tree of `n` nodes (undirected, labeled 0 to n-1), find all roots that minimize the tree's height. Return the list of root labels.

```
Input:  n = 4, edges = [[1,0],[1,2],[1,3]]
Output: [1]

Input:  n = 6, edges = [[3,0],[3,1],[3,2],[3,4],[5,4]]
Output: [3,4]
```

### Intuition

The optimal root(s) are always at the **center** of the tree (1 or 2 nodes). Find them by repeatedly removing leaf nodes (nodes with degree 1) — like peeling an onion from outside in — until 1 or 2 nodes remain.

This is topological sort applied to an undirected tree.

### Solution

```python
from collections import deque

def findMinHeightTrees(n: int, edges: list[list[int]]) -> list[int]:
    if n == 1:
        return [0]

    adj = [set() for _ in range(n)]
    for u, v in edges:
        adj[u].add(v)
        adj[v].add(u)

    # Start with all leaf nodes
    leaves = deque(i for i in range(n) if len(adj[i]) == 1)
    remaining = n

    while remaining > 2:
        leaf_count = len(leaves)
        remaining -= leaf_count
        for _ in range(leaf_count):
            leaf = leaves.popleft()
            neighbor = adj[leaf].pop()
            adj[neighbor].remove(leaf)
            if len(adj[neighbor]) == 1:
                leaves.append(neighbor)

    return list(leaves)
```

**Time:** O(n) | **Space:** O(n)

### Interview Traps

**Trap 1 — Trying every node as root (O(n²))**
BFS from every node to find minimum height is O(n²). The leaf-trimming approach is O(n).

**Trap 2 — There are always at most 2 centroid nodes**
A tree's centroid is always 1 or 2 nodes — never more. The algorithm terminates when `remaining <= 2`.

**Trap 3 — n = 1 edge case**
With 1 node and no edges, the only root is 0. Handle before building the adjacency list.

**Trap 4 — Confusing with finding the tree's center for other problems**
The centroid here minimizes height, not edge distances. Different from the weighted centroid used in some graph problems.

---

## 5. Bus Routes

**LeetCode #815 | Difficulty: 🔴 Hard**

### Problem Statement

You are given an array of bus routes where `routes[i]` is a list of bus stops that bus `i` travels. You start at stop `source` and want to reach stop `target`. Return the minimum number of buses you must take, or `-1` if impossible.

```
Input:  routes = [[1,2,7],[3,6,7]], source = 1, target = 6
Output: 2
```

### Intuition

BFS where each **node is a bus stop** and each **edge is a bus route** connection. But naively connecting all stops on the same route is O(stops²). 

Better: BFS on **routes**, not stops. Map each stop to the routes that serve it. Start by boarding all routes serving `source`. When exploring a route, visit all its stops — if any is `target`, return. If not, enqueue all routes serving those stops.

### Solution

```python
from collections import defaultdict, deque

def numBusesToDestination(routes: list[list[int]], source: int, target: int) -> int:
    if source == target:
        return 0

    # Map stop → list of routes serving it
    stop_to_routes = defaultdict(set)
    for i, route in enumerate(routes):
        for stop in route:
            stop_to_routes[stop].add(i)

    # BFS on routes
    visited_stops = {source}
    visited_routes = set()
    queue = deque([(source, 0)])  # (stop, buses_taken)

    while queue:
        stop, buses = queue.popleft()

        for route_id in stop_to_routes[stop]:
            if route_id in visited_routes:
                continue
            visited_routes.add(route_id)

            for next_stop in routes[route_id]:
                if next_stop == target:
                    return buses + 1
                if next_stop not in visited_stops:
                    visited_stops.add(next_stop)
                    queue.append((next_stop, buses + 1))

    return -1
```

**Time:** O(N) where N = total stops across all routes | **Space:** O(N)

### Interview Traps

**Trap 1 — BFS on stops directly**
Building a graph where all stops on the same route are connected gives O(stops²) edges. BFS on routes avoids this.

**Trap 2 — Counting stops instead of buses**
The answer is the number of bus changes, not stops visited.

**Trap 3 — Not tracking visited routes**
Without marking routes as visited, you process the same route multiple times exponentially.

**Trap 4 — source == target edge case**
Return 0 immediately — no bus needed.

---

*[← Back to Index](./index.md) | [Next: Hash Next: Hash & Heap Extra →](./07_hash_heap_extra.md) Heap Extra →](./25_hash_heap_extra.md)*

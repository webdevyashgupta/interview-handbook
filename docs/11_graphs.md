# Graphs

**Topic 11 of 18 | NeetCode 150 Interview Handbook**  
[← Back to Index](./index.md)

---

## Problems in this Section

| # | Problem | Difficulty |
|---|---------|------------|
| 80 | [Number of Islands](#80-number-of-islands) | 🟡 Medium |
| 81 | [Clone Graph](#81-clone-graph) | 🟡 Medium |
| 82 | [Max Area of Island](#82-max-area-of-island) | 🟡 Medium |
| 83 | [Pacific Atlantic Water Flow](#83-pacific-atlantic-water-flow) | 🟡 Medium |
| 84 | [Surrounded Regions](#84-surrounded-regions) | 🟡 Medium |
| 85 | [Rotting Oranges](#85-rotting-oranges) | 🟡 Medium |
| 86 | [Walls and Gates](#86-walls-and-gates) | 🟡 Medium |
| 87 | [Course Schedule](#87-course-schedule) | 🟡 Medium |
| 88 | [Course Schedule II](#88-course-schedule-ii) | 🟡 Medium |
| 89 | [Redundant Connection](#89-redundant-connection) | 🟡 Medium |
| 90 | [Number of Connected Components](#90-number-of-connected-components) | 🟡 Medium |
| 91 | [Graph Valid Tree](#91-graph-valid-tree) | 🟡 Medium |
| 92 | [Word Ladder](#92-word-ladder) | 🔴 Hard |

---

## 80. Number of Islands

**LeetCode #200 | Difficulty: 🟡 Medium**

### Problem Statement

Given an `m x n` 2D binary grid where `'1'` represents land and `'0'` represents water, return the number of islands.

```
Input:
  11110
  11010
  11000
  00000
Output: 1
```

### Intuition

For each unvisited `'1'` cell, run DFS/BFS to mark all connected land cells as visited. Count how many times you start a new DFS — that's the number of islands.

### Solution

```python
def numIslands(grid: list[list[str]]) -> int:
    rows, cols = len(grid), len(grid[0])
    count = 0

    def dfs(r, c):
        if r < 0 or r >= rows or c < 0 or c >= cols or grid[r][c] != '1':
            return
        grid[r][c] = '0'  # mark visited
        dfs(r+1, c); dfs(r-1, c); dfs(r, c+1); dfs(r, c-1)

    for r in range(rows):
        for c in range(cols):
            if grid[r][c] == '1':
                count += 1
                dfs(r, c)

    return count
```

**Time:** O(m·n) | **Space:** O(m·n) recursion stack

### Interview Traps

**Trap 1 — Not marking cells as visited**
Without marking, the DFS revisits cells infinitely → infinite recursion.

**Trap 2 — "Can you avoid modifying the grid?"**
Use a separate `visited` set of `(r, c)` tuples. Uses O(m·n) extra space.

**Trap 3 — BFS alternative**
Use a queue instead of recursion — avoids stack overflow for large grids.

**Trap 4 — Diagonal connectivity**
This problem uses 4-directional connectivity (up/down/left/right), not 8-directional. Clarify if asked.

---

## 81. Clone Graph

**LeetCode #133 | Difficulty: 🟡 Medium**

### Problem Statement

Given a reference to a node in a connected undirected graph, return a deep copy (clone) of the graph.

### Intuition

Use a **HashMap** mapping original nodes to their clones. DFS/BFS through the graph — for each node, create its clone if it doesn't exist, then recursively clone all neighbors.

### Solution

```python
def cloneGraph(node):
    if not node:
        return None

    cloned = {}

    def dfs(n):
        if n in cloned:
            return cloned[n]
        copy = Node(n.val)
        cloned[n] = copy
        for neighbor in n.neighbors:
            copy.neighbors.append(dfs(neighbor))
        return copy

    return dfs(node)
```

**Time:** O(V + E) | **Space:** O(V)

### Interview Traps

**Trap 1 — Not handling cycles**
Graphs can have cycles. The `cloned` HashMap prevents infinite recursion by returning the already-cloned node.

**Trap 2 — Creating a clone before storing in map**
Store the clone in the map BEFORE recursing into neighbors — otherwise cycles cause infinite loops.

**Trap 3 — "What if the graph is disconnected?"**
The problem states it's connected. If it weren't, you'd need to iterate over all nodes.

---

## 82. Max Area of Island

**LeetCode #695 | Difficulty: 🟡 Medium**

### Problem Statement

Given a binary matrix `grid`, return the maximum area of an island (group of connected `1`s). Return 0 if no island exists.

```
Input:  [[0,0,1,0,0],[0,1,1,1,0],[0,0,0,0,0]]
Output: 4
```

### Intuition

Same as Number of Islands but instead of counting islands, count the size of each island and track the maximum.

### Solution

```python
def maxAreaOfIsland(grid: list[list[int]]) -> int:
    rows, cols = len(grid), len(grid[0])

    def dfs(r, c):
        if r < 0 or r >= rows or c < 0 or c >= cols or grid[r][c] != 1:
            return 0
        grid[r][c] = 0  # mark visited
        return 1 + dfs(r+1,c) + dfs(r-1,c) + dfs(r,c+1) + dfs(r,c-1)

    return max(dfs(r, c) for r in range(rows) for c in range(cols))
```

**Time:** O(m·n) | **Space:** O(m·n)

### Interview Traps

**Trap 1 — Returning early when area > current max**
You can't prune early here — you must fully explore the island to count it correctly (or reset visited incorrectly).

**Trap 2 — Not marking cells during DFS**
Without marking, cells are counted multiple times.

**Trap 3 — Initializing max to 0**
`max(dfs(...))` over all cells is clean — if no island exists, all DFS calls return 0.

---

## 83. Pacific Atlantic Water Flow

**LeetCode #417 | Difficulty: 🟡 Medium**

### Problem Statement

Given an `m x n` matrix of heights, find all cells from which water can flow to both the Pacific ocean (top/left edges) and the Atlantic ocean (bottom/right edges).

```
Input:  heights = [[1,2,2,3,5],[3,2,3,4,4],[2,4,5,3,1],[6,7,1,4,5],[5,1,1,2,4]]
Output: [[0,4],[1,3],[1,4],[2,2],[3,0],[3,1],[4,0]]
```

### Intuition

Reverse the problem: instead of flowing downward from cells to oceans, **flow upward from oceans to cells**. BFS/DFS from all Pacific-adjacent cells and all Atlantic-adjacent cells. A cell reachable from both oceans is in the answer.

### Solution

```python
from collections import deque

def pacificAtlantic(heights: list[list[int]]) -> list[list[int]]:
    rows, cols = len(heights), len(heights[0])

    def bfs(starts):
        visited = set(starts)
        queue = deque(starts)
        while queue:
            r, c = queue.popleft()
            for dr, dc in [(0,1),(0,-1),(1,0),(-1,0)]:
                nr, nc = r + dr, c + dc
                if (0 <= nr < rows and 0 <= nc < cols and
                    (nr, nc) not in visited and
                    heights[nr][nc] >= heights[r][c]):
                    visited.add((nr, nc))
                    queue.append((nr, nc))
        return visited

    pacific_starts = [(r, 0) for r in range(rows)] + [(0, c) for c in range(cols)]
    atlantic_starts = [(r, cols-1) for r in range(rows)] + [(rows-1, c) for c in range(cols)]

    pacific = bfs(pacific_starts)
    atlantic = bfs(atlantic_starts)

    return [[r, c] for r, c in pacific & atlantic]
```

**Time:** O(m·n) | **Space:** O(m·n)

### Interview Traps

**Trap 1 — Flowing from cells to oceans (forward direction)**
This requires checking all 4 neighbors and is complex. The reverse BFS from ocean edges is cleaner.

**Trap 2 — Wrong flow condition in reverse BFS**
In reverse BFS, water flows *uphill* — only visit neighbors with height ≥ current.  
✅ `heights[nr][nc] >= heights[r][c]`

**Trap 3 — Initializing BFS with all edge cells simultaneously**
Multi-source BFS from all ocean-adjacent cells at once is correct and efficient.

---

## 84. Surrounded Regions

**LeetCode #130 | Difficulty: 🟡 Medium**

### Problem Statement

Given an `m x n` matrix of `'X'` and `'O'`, capture all regions surrounded by `'X'` — replace all surrounded `'O'`s with `'X'`. An `'O'` on the border or connected to a border `'O'` is NOT captured.

```
Input:  [["X","X","X","X"],["X","O","O","X"],["X","X","O","X"],["X","O","X","X"]]
Output: [["X","X","X","X"],["X","X","X","X"],["X","X","X","X"],["X","O","X","X"]]
```

### Intuition

Reverse thinking: instead of finding surrounded `'O'`s, find **unsurrounded** `'O'`s — those connected to the border. Mark them as `'T'` (temporary). Then:
1. All remaining `'O'`s are surrounded → convert to `'X'`.
2. All `'T'`s are safe → convert back to `'O'`.

### Solution

```python
def solve(board: list[list[str]]) -> None:
    rows, cols = len(board), len(board[0])

    def dfs(r, c):
        if r < 0 or r >= rows or c < 0 or c >= cols or board[r][c] != 'O':
            return
        board[r][c] = 'T'
        dfs(r+1,c); dfs(r-1,c); dfs(r,c+1); dfs(r,c-1)

    # Mark border-connected O's
    for r in range(rows):
        dfs(r, 0); dfs(r, cols-1)
    for c in range(cols):
        dfs(0, c); dfs(rows-1, c)

    # Flip all remaining O's to X, restore T's to O
    for r in range(rows):
        for c in range(cols):
            if board[r][c] == 'O':
                board[r][c] = 'X'
            elif board[r][c] == 'T':
                board[r][c] = 'O'
```

**Time:** O(m·n) | **Space:** O(m·n)

### Interview Traps

**Trap 1 — Trying to find surrounded regions directly**
Finding enclosed regions is complex. The reverse approach (mark safe, flip the rest) is much cleaner.

**Trap 2 — Forgetting to restore 'T' back to 'O'**
After the final pass, `'T'` cells must become `'O'` again.

**Trap 3 — Missing corner cells in border scan**
Scan all four borders — top row, bottom row, left column, right column.

---

## 85. Rotting Oranges

**LeetCode #994 | Difficulty: 🟡 Medium**

### Problem Statement

A grid contains `0` (empty), `1` (fresh orange), `2` (rotten orange). Each minute, rotten oranges infect adjacent fresh ones. Return the minimum minutes until no fresh orange remains, or `-1` if impossible.

```
Input:  [[2,1,1],[1,1,0],[0,1,1]]
Output: 4
```

### Intuition

**Multi-source BFS** starting from all rotten oranges simultaneously. Each BFS level = 1 minute. Count fresh oranges; decrement as they're infected. If fresh count reaches 0 → return elapsed time. If fresh remains after BFS → return -1.

### Solution

```python
from collections import deque

def orangesRotting(grid: list[list[int]]) -> int:
    rows, cols = len(grid), len(grid[0])
    fresh = 0
    queue = deque()

    for r in range(rows):
        for c in range(cols):
            if grid[r][c] == 1:
                fresh += 1
            elif grid[r][c] == 2:
                queue.append((r, c))

    time = 0
    while queue and fresh > 0:
        time += 1
        for _ in range(len(queue)):
            r, c = queue.popleft()
            for dr, dc in [(0,1),(0,-1),(1,0),(-1,0)]:
                nr, nc = r + dr, c + dc
                if 0 <= nr < rows and 0 <= nc < cols and grid[nr][nc] == 1:
                    grid[nr][nc] = 2
                    fresh -= 1
                    queue.append((nr, nc))

    return time if fresh == 0 else -1
```

**Time:** O(m·n) | **Space:** O(m·n)

### Interview Traps

**Trap 1 — BFS from one rotten orange at a time**
All rotten oranges rot simultaneously — use multi-source BFS (add all initial rotten oranges to queue at once).

**Trap 2 — Returning `time` without checking `fresh == 0`**
Isolated fresh oranges can never be reached. Always return `-1` if `fresh > 0` after BFS.

**Trap 3 — Counting time when queue is empty at start**
If there are no fresh oranges initially, return 0 (not -1). The `while fresh > 0` condition handles this.

---

## 86. Walls and Gates

**LeetCode #286 | Difficulty: 🟡 Medium**

### Problem Statement

Fill each empty room in a grid with the distance to its nearest gate. `INF` = empty room, `-1` = wall, `0` = gate.

### Intuition

Multi-source BFS from all gates simultaneously. Each BFS step increments the distance. Empty rooms are filled with the BFS level they're first reached at — guaranteeing the shortest distance.

### Solution

```python
from collections import deque

def wallsAndGates(rooms: list[list[int]]) -> None:
    INF = 2**31 - 1
    rows, cols = len(rooms), len(rooms[0])
    queue = deque()

    for r in range(rows):
        for c in range(cols):
            if rooms[r][c] == 0:
                queue.append((r, c))

    dist = 0
    while queue:
        dist += 1
        for _ in range(len(queue)):
            r, c = queue.popleft()
            for dr, dc in [(0,1),(0,-1),(1,0),(-1,0)]:
                nr, nc = r + dr, c + dc
                if 0 <= nr < rows and 0 <= nc < cols and rooms[nr][nc] == INF:
                    rooms[nr][nc] = dist
                    queue.append((nr, nc))
```

**Time:** O(m·n) | **Space:** O(m·n)

### Interview Traps

**Trap 1 — BFS from each room to find the nearest gate**
O(m·n) BFS per room = O((m·n)²). Multi-source BFS from gates is O(m·n).

**Trap 2 — Not skipping walls**
Only update `INF` rooms — skip `0` (gates) and `-1` (walls).

---

## 87. Course Schedule

**LeetCode #207 | Difficulty: 🟡 Medium**

### Problem Statement

Given `numCourses` and a list of prerequisites `[a, b]` (must take b before a), return `true` if it's possible to finish all courses.

```
Input:  numCourses = 2, prerequisites = [[1,0]]
Output: true

Input:  numCourses = 2, prerequisites = [[1,0],[0,1]]
Output: false  (cycle!)
```

### Intuition

This is a **cycle detection** problem on a directed graph. Build an adjacency list. Run DFS — if we ever revisit a node currently in the DFS path, there's a cycle → return false.

Use three states: `0` = unvisited, `1` = in current path (grey), `2` = fully processed (black).

### Solution

```python
def canFinish(numCourses: int, prerequisites: list[list[int]]) -> bool:
    graph = [[] for _ in range(numCourses)]
    for a, b in prerequisites:
        graph[b].append(a)

    state = [0] * numCourses  # 0=unvisited, 1=visiting, 2=done

    def dfs(node):
        if state[node] == 1:
            return False  # cycle detected
        if state[node] == 2:
            return True   # already processed

        state[node] = 1
        for neighbor in graph[node]:
            if not dfs(neighbor):
                return False
        state[node] = 2
        return True

    return all(dfs(i) for i in range(numCourses))
```

**Time:** O(V + E) | **Space:** O(V + E)

### Interview Traps

**Trap 1 — Using a simple visited set**
A visited set can't distinguish "currently in path" from "already processed" — causing false cycle detection.  
✅ Use three states (0, 1, 2) or grey/black coloring.

**Trap 2 — Not handling disconnected graphs**
Run DFS from every node — not just node 0.

**Trap 3 — "Topological sort" alternative**
Kahn's algorithm (BFS with in-degree tracking) also detects cycles — if not all nodes are in the topological order, a cycle exists.

---

## 88. Course Schedule II

**LeetCode #210 | Difficulty: 🟡 Medium**

### Problem Statement

Return the ordering of courses to finish all courses, or an empty array if impossible.

### Intuition

Same as Course Schedule I, but now perform **topological sort** (DFS-based). After fully processing a node, append it to the result. Reverse the result at the end.

### Solution

```python
def findOrder(numCourses: int, prerequisites: list[list[int]]) -> list[int]:
    graph = [[] for _ in range(numCourses)]
    for a, b in prerequisites:
        graph[b].append(a)

    state = [0] * numCourses
    result = []

    def dfs(node):
        if state[node] == 1: return False
        if state[node] == 2: return True
        state[node] = 1
        for nb in graph[node]:
            if not dfs(nb): return False
        state[node] = 2
        result.append(node)
        return True

    for i in range(numCourses):
        if not dfs(i):
            return []

    return result[::-1]
```

**Time:** O(V + E) | **Space:** O(V + E)

### Interview Traps

**Trap 1 — Forgetting to reverse**
DFS appends nodes in finish order (deepest first). Reverse to get the correct topological order.

**Trap 2 — Kahn's Algorithm alternative**
```python
from collections import deque
in_degree = [0] * numCourses
for a, b in prerequisites: in_degree[a] += 1
queue = deque(i for i in range(numCourses) if in_degree[i] == 0)
result = []
while queue:
    node = queue.popleft()
    result.append(node)
    for nb in graph[node]:
        in_degree[nb] -= 1
        if in_degree[nb] == 0: queue.append(nb)
return result if len(result) == numCourses else []
```

---

## 89. Redundant Connection

**LeetCode #684 | Difficulty: 🟡 Medium**

### Problem Statement

Given a tree with n nodes and one extra edge (making it a graph with a cycle), return the redundant edge that, when removed, makes it a tree again.

```
Input:  edges = [[1,2],[1,3],[2,3]]
Output: [2,3]
```

### Intuition

Use **Union-Find (Disjoint Set Union)**. Process edges one by one. For each edge `(u, v)`:
- If `u` and `v` are already in the same component → this edge creates a cycle → return it.
- Otherwise, union them.

### Solution

```python
def findRedundantConnection(edges: list[list[int]]) -> list[int]:
    n = len(edges)
    parent = list(range(n + 1))
    rank = [0] * (n + 1)

    def find(x):
        if parent[x] != x:
            parent[x] = find(parent[x])  # path compression
        return parent[x]

    def union(x, y):
        px, py = find(x), find(y)
        if px == py:
            return False  # same component → cycle
        if rank[px] < rank[py]:
            px, py = py, px
        parent[py] = px
        if rank[px] == rank[py]:
            rank[px] += 1
        return True

    for u, v in edges:
        if not union(u, v):
            return [u, v]

    return []
```

**Time:** O(n · α(n)) ≈ O(n) | **Space:** O(n)

### Interview Traps

**Trap 1 — Using DFS cycle detection instead of Union-Find**
DFS works but is O(n) per edge → O(n²) total. Union-Find is nearly O(1) per operation.

**Trap 2 — Not using path compression and union by rank**
Without optimization, find() is O(n) per call. Path compression + rank gives amortized O(α(n)).

**Trap 3 — Returning the last edge found instead of the correct redundant edge**
Process edges in order — the first edge that creates a cycle is the answer (last edge in the cycle).

---

## 90. Number of Connected Components

**LeetCode #323 | Difficulty: 🟡 Medium**

### Problem Statement

Given n nodes (0 to n-1) and a list of undirected edges, return the number of connected components.

```
Input:  n = 5, edges = [[0,1],[1,2],[3,4]]
Output: 2
```

### Intuition

Use **Union-Find**. Start with n components. For each edge, if the two nodes are in different components, union them and decrement the component count.

### Solution

```python
def countComponents(n: int, edges: list[list[int]]) -> int:
    parent = list(range(n))
    rank = [0] * n
    components = n

    def find(x):
        if parent[x] != x:
            parent[x] = find(parent[x])
        return parent[x]

    def union(x, y):
        nonlocal components
        px, py = find(x), find(y)
        if px == py:
            return
        if rank[px] < rank[py]:
            px, py = py, px
        parent[py] = px
        if rank[px] == rank[py]:
            rank[px] += 1
        components -= 1

    for u, v in edges:
        union(u, v)

    return components
```

**Time:** O(n · α(n)) | **Space:** O(n)

### Interview Traps

**Trap 1 — Using DFS and a visited array**
DFS works and is equally valid here. Union-Find is preferred when edges are added incrementally.

**Trap 2 — Not initializing components = n**
Start with n components (each node is its own component), then decrement with each successful union.

---

## 91. Graph Valid Tree

**LeetCode #261 | Difficulty: 🟡 Medium**

### Problem Statement

Given n nodes (0 to n-1) and a list of edges, determine if the edges form a valid tree.

A valid tree has exactly `n-1` edges and is fully connected (no cycles, no disconnected components).

```
Input:  n = 5, edges = [[0,1],[0,2],[0,3],[1,4]]
Output: true
```

### Intuition

A graph is a valid tree if:
1. It has exactly `n-1` edges (necessary condition).
2. It is fully connected (no cycles, one component).

Use Union-Find: if any edge connects two nodes already in the same component → cycle → not a tree.

### Solution

```python
def validTree(n: int, edges: list[list[int]]) -> bool:
    if len(edges) != n - 1:
        return False

    parent = list(range(n))
    rank = [0] * n

    def find(x):
        if parent[x] != x:
            parent[x] = find(parent[x])
        return parent[x]

    def union(x, y):
        px, py = find(x), find(y)
        if px == py:
            return False
        if rank[px] < rank[py]:
            px, py = py, px
        parent[py] = px
        if rank[px] == rank[py]:
            rank[px] += 1
        return True

    return all(union(u, v) for u, v in edges)
```

**Time:** O(n · α(n)) | **Space:** O(n)

### Interview Traps

**Trap 1 — Not checking edge count first**
`n-1` edges is a necessary condition. An early check saves time.

**Trap 2 — Only checking connectivity, not cycles**
A disconnected graph with no cycles is not a tree. Both conditions must hold.

**Trap 3 — "What's the difference between a tree and a forest?"**
A forest is a collection of trees (acyclic but potentially disconnected). A tree is a connected forest.

---

## 92. Word Ladder

**LeetCode #127 | Difficulty: 🔴 Hard**

### Problem Statement

Given `beginWord`, `endWord`, and a `wordList`, return the number of words in the shortest transformation sequence from `beginWord` to `endWord`. Each step changes exactly one letter, and every intermediate word must be in `wordList`.

```
Input:  beginWord = "hit", endWord = "cog", wordList = ["hot","dot","dog","lot","log","cog"]
Output: 5  (hit→hot→dot→dog→cog)
```

### Intuition

**BFS** for shortest path. From the current word, generate all one-letter mutations and check if they're in the word set. Use BFS levels to count the number of transformations.

Optimization: for each position, try all 26 letters instead of checking every word in the list.

### Solution

```python
from collections import deque

def ladderLength(beginWord: str, endWord: str, wordList: list[str]) -> int:
    word_set = set(wordList)
    if endWord not in word_set:
        return 0

    queue = deque([(beginWord, 1)])
    visited = {beginWord}

    while queue:
        word, length = queue.popleft()
        for i in range(len(word)):
            for c in 'abcdefghijklmnopqrstuvwxyz':
                new_word = word[:i] + c + word[i+1:]
                if new_word == endWord:
                    return length + 1
                if new_word in word_set and new_word not in visited:
                    visited.add(new_word)
                    queue.append((new_word, length + 1))

    return 0
```

**Time:** O(m² · n) where m = word length, n = wordList size | **Space:** O(m · n)

### Interview Traps

**Trap 1 — Using DFS instead of BFS**
DFS finds *a* path, not the *shortest* path. BFS is mandatory for shortest path problems.

**Trap 2 — Not checking if endWord is in wordList**
If `endWord` is not in the word set, it's unreachable → return 0 immediately.

**Trap 3 — Iterating over all words for each neighbor**
O(n) per word. Generate mutations character by character (26 options per position) and check membership in the set — O(26·m) per word.

**Trap 4 — Bidirectional BFS (advanced optimization)**
BFS from both `beginWord` and `endWord` simultaneously — reduces search space from O(b^d) to O(b^(d/2)).

**Trap 5 — Counting words vs edges**
Return the number of words in the sequence (including begin and end), not edges. The `length` variable starts at 1 (counting `beginWord`).

---

*[← Back to Index](./index.md) | [Next: Advanced Graphs →](./12_advanced_graphs.md)*

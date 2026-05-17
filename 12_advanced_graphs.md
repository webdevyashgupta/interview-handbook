# Advanced Graphs

**Topic 12 of 18 | NeetCode 150 Interview Handbook**  
[← Back to Index](./index.md)

---

## Problems in this Section

| # | Problem | Difficulty |
|---|---------|------------|
| 93 | [Reconstruct Itinerary](#93-reconstruct-itinerary) | 🔴 Hard |
| 94 | [Min Cost to Connect All Points](#94-min-cost-to-connect-all-points) | 🟡 Medium |
| 95 | [Network Delay Time](#95-network-delay-time) | 🟡 Medium |
| 96 | [Swim in Rising Water](#96-swim-in-rising-water) | 🔴 Hard |
| 97 | [Alien Dictionary](#97-alien-dictionary) | 🔴 Hard |
| 98 | [Cheapest Flights Within K Stops](#98-cheapest-flights-within-k-stops) | 🟡 Medium |

---

## 93. Reconstruct Itinerary

**LeetCode #332 | Difficulty: 🔴 Hard**

### Problem Statement

Given a list of airline `tickets` represented as `[from, to]` pairs, reconstruct the itinerary in order. Begin from `"JFK"`. If multiple valid itineraries exist, return the one with the smallest lexical order.

```
Input:  tickets = [["MUC","LHR"],["JFK","MUC"],["SFO","SJC"],["LHR","SFO"]]
Output: ["JFK","MUC","LHR","SFO","SJC"]
```

### Intuition

This is an **Eulerian path** problem — visit every edge exactly once. Use **Hierholzer's algorithm**: DFS with a sorted adjacency list. When a node has no more outgoing edges, add it to the result. Reverse at the end.

### Solution

```python
from collections import defaultdict

def findItinerary(tickets: list[list[str]]) -> list[str]:
    graph = defaultdict(list)
    for src, dst in sorted(tickets, reverse=True):
        graph[src].append(dst)

    result = []

    def dfs(airport):
        while graph[airport]:
            dfs(graph[airport].pop())
        result.append(airport)

    dfs("JFK")
    return result[::-1]
```

**Time:** O(E log E) for sorting | **Space:** O(E)

### Interview Traps

**Trap 1 — Standard DFS without Hierholzer's trick**
Standard DFS fails on dead-end branches. Hierholzer's algorithm handles dead ends by adding them to result after backtracking.

**Trap 2 — Lexical order**
Sort tickets so smaller destinations come first (or sort and pop from the end of a reversed list).

**Trap 3 — Not using all tickets**
Every ticket must be used. The DFS must consume all edges.

**Trap 4 — "What if no valid itinerary exists?"**
The problem guarantees one exists. In general, an Eulerian path exists iff the graph is connected and has at most 2 nodes with odd degree.

---

## 94. Min Cost to Connect All Points

**LeetCode #1584 | Difficulty: 🟡 Medium**

### Problem Statement

Given an array of points on a plane, return the minimum cost to connect all points where cost = Manhattan distance between two points. (Minimum Spanning Tree problem.)

```
Input:  points = [[0,0],[2,2],[3,10],[5,2],[7,0]]
Output: 20
```

### Intuition

This is a **Minimum Spanning Tree (MST)** problem. Two classic algorithms:
1. **Prim's** — grow the MST greedily from a starting node using a min-heap.
2. **Kruskal's** — sort all edges by weight and add them with Union-Find.

For dense graphs (where E ≈ V²), Prim's with a heap is O(E log V) and practical.

### Solution (Prim's Algorithm)

```python
import heapq

def minCostConnectPoints(points: list[list[int]]) -> int:
    n = len(points)
    visited = set()
    heap = [(0, 0)]  # (cost, point_index)
    total = 0

    while len(visited) < n:
        cost, i = heapq.heappop(heap)
        if i in visited:
            continue
        visited.add(i)
        total += cost
        for j in range(n):
            if j not in visited:
                dist = abs(points[i][0] - points[j][0]) + abs(points[i][1] - points[j][1])
                heapq.heappush(heap, (dist, j))

    return total
```

**Time:** O(n² log n) | **Space:** O(n²)

### Interview Traps

**Trap 1 — Not recognizing this as an MST problem**
The key insight: connecting all n points with minimum cost = Minimum Spanning Tree.

**Trap 2 — Euclidean vs Manhattan distance**
The problem uses Manhattan distance (`|x1-x2| + |y1-y2|`), not Euclidean (`sqrt(...)`).

**Trap 3 — Kruskal's for dense graphs**
Kruskal's requires sorting all O(n²) edges first — O(n² log n). Prim's can be more efficient with a priority queue for dense graphs.

**Trap 4 — Skipping visited nodes in Prim's**
Always check `if i in visited: continue` after popping from the heap — stale entries may remain.

---

## 95. Network Delay Time

**LeetCode #743 | Difficulty: 🟡 Medium**

### Problem Statement

Given a list of travel `times` as directed edges `[u, v, w]`, a source node `k`, and `n` nodes, return the minimum time for all nodes to receive a signal from `k`. Return `-1` if impossible.

```
Input:  times = [[2,1,1],[2,3,1],[3,4,1]], n = 4, k = 2
Output: 2
```

### Intuition

**Dijkstra's algorithm** — single source shortest paths. The answer is the maximum shortest path to any node. If any node is unreachable, return -1.

### Solution

```python
import heapq
from collections import defaultdict

def networkDelayTime(times: list[list[int]], n: int, k: int) -> int:
    graph = defaultdict(list)
    for u, v, w in times:
        graph[u].append((v, w))

    dist = {}
    heap = [(0, k)]  # (time, node)

    while heap:
        t, node = heapq.heappop(heap)
        if node in dist:
            continue
        dist[node] = t
        for neighbor, weight in graph[node]:
            if neighbor not in dist:
                heapq.heappush(heap, (t + weight, neighbor))

    return max(dist.values()) if len(dist) == n else -1
```

**Time:** O((V + E) log V) | **Space:** O(V + E)

### Interview Traps

**Trap 1 — Using BFS (assumes uniform weights)**
BFS gives shortest path only for unweighted graphs. Dijkstra's handles weighted edges.

**Trap 2 — Bellman-Ford alternative**
Bellman-Ford handles negative weights — Dijkstra's doesn't. For this problem, weights are positive, so Dijkstra's is optimal.

**Trap 3 — Returning max of dist without checking all n nodes**
If some node is unreachable, `len(dist) < n` → return -1.

**Trap 4 — Not handling duplicate heap entries**
Stale entries in the heap may appear. Use `if node in dist: continue` to skip them.

---

## 96. Swim in Rising Water

**LeetCode #778 | Difficulty: 🔴 Hard**

### Problem Statement

Given an `n x n` grid where `grid[r][c]` is the elevation at that cell, find the minimum time `t` such that you can travel from `(0,0)` to `(n-1,n-1)`. At time `t`, you can swim through any cell with elevation ≤ t.

```
Input:  grid = [[0,2],[1,3]]
Output: 3
```

### Intuition

**Modified Dijkstra** (or binary search + BFS). Think of it as finding the path from `(0,0)` to `(n-1,n-1)` that **minimizes the maximum elevation** along the path. Use a min-heap where the priority is the maximum elevation seen so far on the path.

### Solution

```python
import heapq

def swimInWater(grid: list[list[int]]) -> int:
    n = len(grid)
    heap = [(grid[0][0], 0, 0)]  # (max_elevation, r, c)
    visited = set()

    while heap:
        t, r, c = heapq.heappop(heap)
        if (r, c) in visited:
            continue
        visited.add((r, c))
        if r == n-1 and c == n-1:
            return t
        for dr, dc in [(0,1),(0,-1),(1,0),(-1,0)]:
            nr, nc = r + dr, c + dc
            if 0 <= nr < n and 0 <= nc < n and (nr, nc) not in visited:
                heapq.heappush(heap, (max(t, grid[nr][nc]), nr, nc))

    return -1
```

**Time:** O(n² log n) | **Space:** O(n²)

### Interview Traps

**Trap 1 — Treating this as a standard shortest-path problem**
The cost is not the sum of elevations — it's the **maximum** elevation along the path. The heap priority must use `max(t, grid[nr][nc])`.

**Trap 2 — Binary search + BFS alternative**
Binary search on answer `t` (0 to n²-1), BFS/DFS to check if `(n-1,n-1)` is reachable using only cells ≤ t. O(n² log n²).

**Trap 3 — Not skipping visited cells**
Without a visited check, the heap grows unboundedly.

---

## 97. Alien Dictionary

**LeetCode #269 | Difficulty: 🔴 Hard**

### Problem Statement

Given a sorted list of words in an alien language, derive the character ordering used in that language. Return any valid ordering, or `""` if invalid (cycle detected).

```
Input:  words = ["wrt","wrf","er","ett","rftt"]
Output: "wertf"
```

### Intuition

Build a directed graph from character ordering derived by comparing adjacent words. For each pair of adjacent words, find the first differing character — that gives an ordering constraint. Then topological sort the graph.

### Solution

```python
from collections import defaultdict, deque

def alienOrder(words: list[str]) -> str:
    # Initialize all chars
    adj = {c: set() for word in words for c in word}
    in_degree = {c: 0 for c in adj}

    # Build edges from adjacent word pairs
    for i in range(len(words) - 1):
        w1, w2 = words[i], words[i+1]
        min_len = min(len(w1), len(w2))
        # Invalid: "abc" before "ab"
        if len(w1) > len(w2) and w1[:min_len] == w2[:min_len]:
            return ""
        for j in range(min_len):
            if w1[j] != w2[j]:
                if w2[j] not in adj[w1[j]]:
                    adj[w1[j]].add(w2[j])
                    in_degree[w2[j]] += 1
                break

    # Topological sort (Kahn's)
    queue = deque(c for c in in_degree if in_degree[c] == 0)
    result = []
    while queue:
        c = queue.popleft()
        result.append(c)
        for nb in adj[c]:
            in_degree[nb] -= 1
            if in_degree[nb] == 0:
                queue.append(nb)

    return "".join(result) if len(result) == len(in_degree) else ""
```

**Time:** O(C) where C = total characters | **Space:** O(1) — at most 26 chars

### Interview Traps

**Trap 1 — Comparing all characters, not just the first difference**
Only the first differing character between adjacent words gives ordering information.

**Trap 2 — Missing the invalid prefix case**
If `word1 = "abc"` appears before `word2 = "ab"`, this is invalid (longer word cannot precede shorter if prefix matches).

**Trap 3 — Cycle detection**
If `len(result) != len(in_degree)` after topological sort → cycle → return `""`.

**Trap 4 — Not initializing all characters**
Every character must appear in `in_degree`, even if it has no incoming edges.

---

## 98. Cheapest Flights Within K Stops

**LeetCode #787 | Difficulty: 🟡 Medium**

### Problem Statement

There are `n` cities connected by flights. Given `flights = [from, to, price]`, find the cheapest price from `src` to `dst` with at most `k` stops. Return `-1` if impossible.

```
Input:  n=4, flights=[[0,1,100],[1,2,100],[2,0,100],[1,3,600],[2,3,200]], src=0, dst=3, k=1
Output: 700
```

### Intuition

**Bellman-Ford with at most k+1 edges** (k stops = k+1 edges). Run k+1 relaxation passes, each considering one more hop. Use a copy of the distances array at each pass to prevent using more than one new edge per pass.

### Solution

```python
def findCheapestPrice(n: int, flights: list[list[int]], src: int, dst: int, k: int) -> int:
    prices = [float('inf')] * n
    prices[src] = 0

    for _ in range(k + 1):  # at most k+1 edges
        temp = prices.copy()
        for u, v, w in flights:
            if prices[u] != float('inf') and prices[u] + w < temp[v]:
                temp[v] = prices[u] + w
        prices = temp

    return prices[dst] if prices[dst] != float('inf') else -1
```

**Time:** O(k · E) | **Space:** O(n)

### Interview Traps

**Trap 1 — Using standard Dijkstra's**
Dijkstra's doesn't constrain the number of hops. Bellman-Ford with k iterations enforces the stop limit.

**Trap 2 — Not copying prices before each pass**
Without copying, a single pass can use multiple new edges (chain reactions), violating the k-stop constraint.  
✅ `temp = prices.copy()` before the inner loop.

**Trap 3 — k stops vs k+1 edges**
k stops = k intermediate cities = k+1 edges total. Run exactly `k+1` relaxation passes.

**Trap 4 — Dijkstra with (cost, stops, node) state**
An alternative is modified Dijkstra tracking `(cost, stops, node)` and pruning when `stops > k`. Valid but more complex.

---

*[← Back to Index](./index.md) | [Next: 1-D Dynamic Programming →](./13_dp_1d.md)*

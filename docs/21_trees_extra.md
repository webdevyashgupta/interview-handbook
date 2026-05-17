# Trees — Extra Problems

**Grind 169 Supplement | Not in NeetCode 150**  
[← Back to Index](./index.md)

---

## Problems in this Section

| # | Problem | Difficulty |
|---|---------|------------|
| 1 | [Symmetric Tree](#1-symmetric-tree) | 🟢 Easy |
| 2 | [Lowest Common Ancestor of a Binary Tree](#2-lowest-common-ancestor-of-a-binary-tree) | 🟡 Medium |
| 3 | [Path Sum II](#3-path-sum-ii) | 🟡 Medium |
| 4 | [Binary Tree Zigzag Level Order Traversal](#4-binary-tree-zigzag-level-order-traversal) | 🟡 Medium |
| 5 | [Maximum Width of Binary Tree](#5-maximum-width-of-binary-tree) | 🟡 Medium |
| 6 | [Path Sum III](#6-path-sum-iii) | 🟡 Medium |
| 7 | [All Nodes Distance K in Binary Tree](#7-all-nodes-distance-k-in-binary-tree) | 🟡 Medium |

---

## 1. Symmetric Tree

**LeetCode #101 | Difficulty: 🟢 Easy**

### Problem Statement

Given the root of a binary tree, check whether it is a mirror of itself (i.e., symmetric around its center).

```
Input:  [1,2,2,3,4,4,3]
Output: true

Input:  [1,2,2,null,3,null,3]
Output: false
```

### Intuition

A tree is symmetric if the left subtree is a mirror of the right subtree. Two trees are mirrors if:
- Their root values are equal
- The left subtree of one is a mirror of the right subtree of the other (and vice versa)

### Solution

```python
def isSymmetric(root) -> bool:
    def isMirror(left, right) -> bool:
        if not left and not right:
            return True
        if not left or not right:
            return False
        return (left.val == right.val and
                isMirror(left.left, right.right) and
                isMirror(left.right, right.left))

    return isMirror(root.left, root.right)
```

**Iterative BFS version:**
```python
from collections import deque

def isSymmetric(root) -> bool:
    queue = deque([(root.left, root.right)])
    while queue:
        left, right = queue.popleft()
        if not left and not right:
            continue
        if not left or not right or left.val != right.val:
            return False
        queue.append((left.left, right.right))
        queue.append((left.right, right.left))
    return True
```

**Time:** O(n) | **Space:** O(h)

### Interview Traps

**Trap 1 — Comparing children of the same node instead of mirrored nodes**
Symmetric means `left.left` mirrors `right.right` and `left.right` mirrors `right.left` — not left mirrors left.

**Trap 2 — Confusing with Same Tree**
Same Tree checks if two trees are identical. Symmetric Tree checks if one tree is its own mirror image.

**Trap 3 — Iterative version**
Queue pairs of nodes that should be mirrors. Push children as mirrored pairs.

---

## 2. Lowest Common Ancestor of a Binary Tree

**LeetCode #236 | Difficulty: 🟡 Medium**

### Problem Statement

Given a binary tree (NOT a BST) and two nodes `p` and `q`, find their lowest common ancestor (LCA).

```
Input:  root = [3,5,1,6,2,0,8,null,null,7,4], p = 5, q = 1
Output: 3

Input:  root = [3,5,1,6,2,0,8,null,null,7,4], p = 5, q = 4
Output: 5
```

### Intuition

Post-order DFS. For each node:
- If the node is `p` or `q`, return it.
- Recurse left and right.
- If both sides return non-null, the current node is the LCA (p and q are in different subtrees).
- If only one side returns non-null, bubble it up.

### Solution

```python
def lowestCommonAncestor(root, p, q):
    if not root or root == p or root == q:
        return root

    left = lowestCommonAncestor(root.left, p, q)
    right = lowestCommonAncestor(root.right, p, q)

    if left and right:
        return root  # p and q are in different subtrees
    return left or right  # one of them contains both
```

**Time:** O(n) | **Space:** O(h)

### Interview Traps

**Trap 1 — Confusing with LCA of BST**
BST LCA uses the BST ordering property → O(h). General binary tree LCA needs full DFS → O(n). Make the distinction clear.

**Trap 2 — Not handling the case where p is an ancestor of q**
If `root == p`, we return `p` immediately — this correctly handles `p` being an ancestor of `q` because the LCA is `p` itself.

**Trap 3 — "What if p or q doesn't exist in the tree?"**
The current solution assumes both exist (guaranteed by problem). If not guaranteed, track a `found_p` and `found_q` boolean.

**Trap 4 — Iterative with parent pointers**
Build a parent map via BFS, then traverse ancestors of `p` into a set, then walk `q`'s ancestors until a match is found. O(n) time and space.

---

## 3. Path Sum II

**LeetCode #113 | Difficulty: 🟡 Medium**

### Problem Statement

Given the root of a binary tree and a `targetSum`, return all root-to-leaf paths where the sum of node values equals `targetSum`.

```
Input:  root = [5,4,8,11,null,13,4,7,2,null,null,5,1], targetSum = 22
Output: [[5,4,11,2],[5,8,4,5]]
```

### Intuition

DFS backtracking. At each node, add it to the current path and subtract its value from the remaining target. When a leaf is reached and the remaining target is 0, record the path. Backtrack by removing the node on return.

### Solution

```python
def pathSum(root, targetSum: int) -> list[list[int]]:
    result = []

    def dfs(node, remaining, path):
        if not node:
            return
        path.append(node.val)
        remaining -= node.val

        if not node.left and not node.right and remaining == 0:
            result.append(list(path))  # copy!

        dfs(node.left, remaining, path)
        dfs(node.right, remaining, path)
        path.pop()  # backtrack

    dfs(root, targetSum, [])
    return result
```

**Time:** O(n²) — O(n) nodes × O(n) path copy | **Space:** O(h)

### Interview Traps

**Trap 1 — Not copying path when adding to result**
`result.append(path)` stores a reference — it will be empty by the end. Always `list(path)`.

**Trap 2 — Checking at non-leaf nodes**
Only record paths at **leaf** nodes (both children are None). A node with one child is not a leaf.

**Trap 3 — Confusing with Path Sum I (just True/False)**
Path Sum I returns a boolean. Path Sum II returns all paths. Different return types and collection logic.

**Trap 4 — Forgetting to pop from path (backtrack)**
Without `path.pop()`, the path accumulates all visited nodes, not just the current root-to-node path.

---

## 4. Binary Tree Zigzag Level Order Traversal

**LeetCode #103 | Difficulty: 🟡 Medium**

### Problem Statement

Given the root of a binary tree, return the zigzag level order traversal of its nodes' values (left to right, then right to left, alternating).

```
Input:  [3,9,20,null,null,15,7]
Output: [[3],[20,9],[15,7]]
```

### Intuition

BFS level order traversal with a direction flag. For even levels, append left to right. For odd levels, append right to left (or use `deque` to append to the front).

### Solution

```python
from collections import deque

def zigzagLevelOrder(root) -> list[list[int]]:
    if not root:
        return []

    result = []
    queue = deque([root])
    left_to_right = True

    while queue:
        level = deque()
        for _ in range(len(queue)):
            node = queue.popleft()
            if left_to_right:
                level.append(node.val)
            else:
                level.appendleft(node.val)
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)

        result.append(list(level))
        left_to_right = not left_to_right

    return result
```

**Time:** O(n) | **Space:** O(n)

### Interview Traps

**Trap 1 — Reversing alternate levels after collecting**
`level.reverse()` for odd levels works but is O(n) extra per level. Using `appendleft` to a deque is O(1) per insertion.

**Trap 2 — Off-by-one on which direction is first**
Root level (level 0) is left-to-right. Initialize `left_to_right = True`.

**Trap 3 — Confusing with Binary Tree Right Side View**
Right Side View takes only the last node per level. Zigzag collects all nodes per level but in alternating direction.

---

## 5. Maximum Width of Binary Tree

**LeetCode #662 | Difficulty: 🟡 Medium**

### Problem Statement

Given the root of a binary tree, return the maximum width of the tree. Width is defined as the number of nodes between the leftmost and rightmost non-null nodes at each level (including null nodes in between).

```
Input:  [1,3,2,5,3,null,9]
Output: 4  (level 2: nodes 5,3,null,9 → width 4)
```

### Intuition

BFS with **index tracking**. Assign each node an index as in a heap: if parent has index `i`, left child is `2i` and right child is `2i+1`. At each level, width = `last_index - first_index + 1`.

Normalize indices at each level to prevent integer overflow.

### Solution

```python
from collections import deque

def widthOfBinaryTree(root) -> int:
    if not root:
        return 0

    max_width = 0
    queue = deque([(root, 0)])  # (node, index)

    while queue:
        level_size = len(queue)
        _, first_idx = queue[0]
        last_idx = first_idx

        for _ in range(level_size):
            node, idx = queue.popleft()
            last_idx = idx
            # Normalize by subtracting first_idx to prevent overflow
            if node.left:
                queue.append((node.left, 2 * (idx - first_idx)))
            if node.right:
                queue.append((node.right, 2 * (idx - first_idx) + 1))

        max_width = max(max_width, last_idx - first_idx + 1)

    return max_width
```

**Time:** O(n) | **Space:** O(n)

### Interview Traps

**Trap 1 — Not normalizing indices**
Without normalization, indices grow exponentially (2^depth) and overflow 32-bit integers in Java/C++. Subtract `first_idx` at each level.

**Trap 2 — Counting only non-null nodes in width**
Width includes null nodes between leftmost and rightmost. The index-based approach handles this correctly.

**Trap 3 — Using node count instead of index difference**
`len(queue)` after processing a level gives the count of non-null nodes, not the width.

---

## 6. Path Sum III

**LeetCode #437 | Difficulty: 🟡 Medium**

### Problem Statement

Given the root of a binary tree and integer `targetSum`, return the number of paths where the sum equals `targetSum`. Paths do not need to start at the root or end at a leaf.

```
Input:  root = [10,5,-3,3,2,null,11,3,-2,null,1], targetSum = 8
Output: 3
```

### Intuition

Use **prefix sums** (same pattern as Subarray Sum Equals K, but on a tree). Maintain a running prefix sum from root to current node. At each node, check how many previous prefix sums equal `current_prefix - targetSum`.

Use a HashMap of `prefix_sum → count` and DFS with backtracking.

### Solution

```python
from collections import defaultdict

def pathSum(root, targetSum: int) -> int:
    count = 0
    prefix_counts = defaultdict(int)
    prefix_counts[0] = 1

    def dfs(node, curr_sum):
        nonlocal count
        if not node:
            return
        curr_sum += node.val
        count += prefix_counts[curr_sum - targetSum]
        prefix_counts[curr_sum] += 1
        dfs(node.left, curr_sum)
        dfs(node.right, curr_sum)
        prefix_counts[curr_sum] -= 1  # backtrack

    dfs(root, 0)
    return count
```

**Time:** O(n) | **Space:** O(n)

### Interview Traps

**Trap 1 — Brute force O(n²)**
Running a new DFS from every node to find paths is O(n²). Prefix sums reduce this to O(n).

**Trap 2 — Not backtracking the prefix map**
When returning from a subtree, decrement the prefix count — otherwise sibling subtrees will incorrectly use paths from other branches.

**Trap 3 — Initializing `prefix_counts[0] = 1`**
This handles paths that start from the root (prefix sum equals targetSum directly).

**Trap 4 — Confusing with Path Sum II**
Path Sum II: root-to-leaf paths only, collect all paths. Path Sum III: any path, just count.

---

## 7. All Nodes Distance K in Binary Tree

**LeetCode #863 | Difficulty: 🟡 Medium**

### Problem Statement

Given the root of a binary tree, a target node, and an integer `k`, return all nodes at distance `k` from the target node.

```
Input:  root = [3,5,1,6,2,0,8,null,null,7,4], target = 5, k = 2
Output: [7,4,1]
```

### Intuition

Convert the tree to an **undirected graph** by building a parent map. Then run BFS from the target node, stopping at distance `k`.

### Solution

```python
from collections import defaultdict, deque

def distanceK(root, target, k: int) -> list[int]:
    # Build parent map via DFS
    parent = {}

    def build_parents(node, par):
        if not node:
            return
        parent[node] = par
        build_parents(node.left, node)
        build_parents(node.right, node)

    build_parents(root, None)

    # BFS from target
    queue = deque([(target, 0)])
    visited = {target}
    result = []

    while queue:
        node, dist = queue.popleft()
        if dist == k:
            result.append(node.val)
            continue
        # Visit children and parent
        for neighbor in [node.left, node.right, parent[node]]:
            if neighbor and neighbor not in visited:
                visited.add(neighbor)
                queue.append((neighbor, dist + 1))

    return result
```

**Time:** O(n) | **Space:** O(n)

### Interview Traps

**Trap 1 — Only traversing downward**
Nodes at distance K can be above the target (via parent). Build the parent map to enable upward traversal.

**Trap 2 — Not tracking visited nodes**
Without a visited set, you'll traverse back and forth infinitely between parent and child.

**Trap 3 — Returning node objects instead of values**
Return `node.val`, not the node itself.

**Trap 4 — "What if k = 0?"**
Return `[target.val]`. The BFS handles this — the first pop has `dist = 0 = k`.

---

*[← Back to Index](./index.md) | [Next: Stack Extra →](./22_stack_extra.md)*

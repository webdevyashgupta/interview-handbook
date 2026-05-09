# Trees

**Topic 7 of 18 | NeetCode 150 Interview Handbook**  
[← Back to Index](./index.md)

---

## Problems in this Section

| # | Problem | Difficulty |
|---|---------|------------|
| 46 | [Invert Binary Tree](#46-invert-binary-tree) | 🟢 Easy |
| 47 | [Maximum Depth of Binary Tree](#47-maximum-depth-of-binary-tree) | 🟢 Easy |
| 48 | [Diameter of Binary Tree](#48-diameter-of-binary-tree) | 🟢 Easy |
| 49 | [Balanced Binary Tree](#49-balanced-binary-tree) | 🟢 Easy |
| 50 | [Same Tree](#50-same-tree) | 🟢 Easy |
| 51 | [Subtree of Another Tree](#51-subtree-of-another-tree) | 🟢 Easy |
| 52 | [Lowest Common Ancestor of a BST](#52-lowest-common-ancestor-of-a-bst) | 🟡 Medium |
| 53 | [Binary Tree Level Order Traversal](#53-binary-tree-level-order-traversal) | 🟡 Medium |
| 54 | [Binary Tree Right Side View](#54-binary-tree-right-side-view) | 🟡 Medium |
| 55 | [Count Good Nodes in Binary Tree](#55-count-good-nodes-in-binary-tree) | 🟡 Medium |
| 56 | [Validate Binary Search Tree](#56-validate-binary-search-tree) | 🟡 Medium |
| 57 | [Kth Smallest Element in a BST](#57-kth-smallest-element-in-a-bst) | 🟡 Medium |
| 58 | [Construct Binary Tree from Preorder and Inorder Traversal](#58-construct-binary-tree-from-preorder-and-inorder-traversal) | 🟡 Medium |
| 59 | [Binary Tree Maximum Path Sum](#59-binary-tree-maximum-path-sum) | 🔴 Hard |
| 60 | [Serialize and Deserialize Binary Tree](#60-serialize-and-deserialize-binary-tree) | 🔴 Hard |

---

## 46. Invert Binary Tree

**LeetCode #226 | Difficulty: 🟢 Easy**

### Problem Statement

Given the root of a binary tree, invert the tree and return its root.

```
Input:
        4
       / \
      2   7
     / \ / \
    1  3 6  9

Output:
        4
       / \
      7   2
     / \ / \
    9  6 3  1
```

### Intuition

Recursively swap the left and right children of every node. A simple post-order or pre-order DFS works — swap children at the current node, then recurse into both subtrees.

### Solution

```python
def invertTree(root):
    if not root:
        return None
    root.left, root.right = root.right, root.left
    invertTree(root.left)
    invertTree(root.right)
    return root
```

**Iterative BFS version:**
```python
from collections import deque

def invertTree(root):
    if not root:
        return None
    queue = deque([root])
    while queue:
        node = queue.popleft()
        node.left, node.right = node.right, node.left
        if node.left:
            queue.append(node.left)
        if node.right:
            queue.append(node.right)
    return root
```

**Time:** O(n) | **Space:** O(h) recursive (h = height), O(n) BFS

### Interview Traps

**Trap 1 — Swapping values instead of pointers**
Swapping `node.left.val` and `node.right.val` only works for simple cases — it fails for subtrees.  
✅ Swap the child pointers themselves: `root.left, root.right = root.right, root.left`.

**Trap 2 — Forgetting the base case**
Always handle `if not root: return None` — recursion terminates at leaves.

**Trap 3 — Order of operations**
Whether you swap before or after recursing doesn't matter here — both pre-order and post-order work for this problem.

**Trap 4 — "Can you do it iteratively?"**
✅ BFS with a queue or DFS with an explicit stack both work. Know at least one iterative approach.

---

## 47. Maximum Depth of Binary Tree

**LeetCode #104 | Difficulty: 🟢 Easy**

### Problem Statement

Given the root of a binary tree, return its maximum depth (number of nodes along the longest path from root to a leaf).

```
Input:  [3,9,20,null,null,15,7]
Output: 3
```

### Intuition

The depth of a tree is `1 + max(depth(left), depth(right))`. Base case: an empty tree has depth 0. This is a natural recursive definition.

### Solution

```python
# Recursive DFS
def maxDepth(root) -> int:
    if not root:
        return 0
    return 1 + max(maxDepth(root.left), maxDepth(root.right))

# Iterative BFS
from collections import deque

def maxDepth(root) -> int:
    if not root:
        return 0
    depth = 0
    queue = deque([root])
    while queue:
        depth += 1
        for _ in range(len(queue)):
            node = queue.popleft()
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
    return depth
```

**Time:** O(n) | **Space:** O(h) recursive, O(n) BFS worst case

### Interview Traps

**Trap 1 — Confusing depth with number of edges**
Depth = number of **nodes** on the longest path, not edges. A single-node tree has depth 1, not 0.

**Trap 2 — Not handling None root**
`maxDepth(None)` should return 0. The base case `if not root: return 0` handles this.

**Trap 3 — BFS level counting**
In BFS, increment `depth` once per level (outer loop), not once per node (inner loop).

**Trap 4 — "What about minimum depth?"**
Minimum depth (LeetCode #111) is trickier — a node with one null child is NOT a leaf. Must reach a node where **both** children are null.

---

## 48. Diameter of Binary Tree

**LeetCode #543 | Difficulty: 🟢 Easy**

### Problem Statement

Given the root of a binary tree, return the length of the diameter — the longest path between any two nodes. The path may or may not pass through the root.

```
Input:  [1,2,3,4,5]
Output: 3  (path: 4→2→1→3 or 5→2→1→3)
```

### Intuition

The diameter through any node = `height(left subtree) + height(right subtree)`. We want the maximum of this across all nodes. Compute height with a DFS, and update a global `max_diameter` at each node.

Key: the diameter doesn't always pass through the root — it passes through the node that maximizes the sum of left+right heights.

### Solution

```python
def diameterOfBinaryTree(root) -> int:
    max_diameter = 0

    def height(node):
        nonlocal max_diameter
        if not node:
            return 0
        left_h = height(node.left)
        right_h = height(node.right)
        max_diameter = max(max_diameter, left_h + right_h)
        return 1 + max(left_h, right_h)

    height(root)
    return max_diameter
```

**Time:** O(n) | **Space:** O(h)

### Interview Traps

**Trap 1 — Computing height separately for each node**
Computing height from scratch at each node leads to O(n²). Combine height computation and diameter update in a single DFS.

**Trap 2 — Assuming diameter always passes through root**
The widest path might be entirely in a subtree. Always track the global maximum, not just the root's diameter.

**Trap 3 — Off-by-one: edges vs nodes**
The diameter is measured in **edges** (number of nodes - 1 on the path). `left_h + right_h` gives the number of edges correctly since height counts nodes.

**Trap 4 — Using a class variable vs nonlocal**
Both work. `nonlocal max_diameter` is clean in Python. Alternatively, use `self.max_diameter` in a class method.

---

## 49. Balanced Binary Tree

**LeetCode #110 | Difficulty: 🟢 Easy**

### Problem Statement

Given a binary tree, determine if it is height-balanced (every node's left and right subtree heights differ by at most 1).

```
Input:  [3,9,20,null,null,15,7]
Output: true

Input:  [1,2,2,3,3,null,null,4,4]
Output: false
```

### Intuition

A naive approach checks balance at every node by computing height separately — O(n²). The optimal approach computes height and checks balance in a single DFS: return `-1` if any subtree is unbalanced, otherwise return its true height.

### Solution

```python
def isBalanced(root) -> bool:
    def check(node):
        if not node:
            return 0
        left = check(node.left)
        if left == -1:
            return -1
        right = check(node.right)
        if right == -1:
            return -1
        if abs(left - right) > 1:
            return -1
        return 1 + max(left, right)

    return check(root) != -1
```

**Time:** O(n) | **Space:** O(h)

### Interview Traps

**Trap 1 — O(n²) approach**
Calling `height()` at every node during a balance check is O(n) per node → O(n²) total.  
✅ Use the sentinel `-1` trick to propagate imbalance in a single pass.

**Trap 2 — Not propagating the -1 sentinel early**
Once a subtree returns `-1`, immediately propagate it upward — don't continue checking.

**Trap 3 — Confusing "balanced" with "complete" or "perfect"**
A balanced tree only requires the height difference to be ≤ 1 at every node. It doesn't need to be full or complete.

---

## 50. Same Tree

**LeetCode #100 | Difficulty: 🟢 Easy**

### Problem Statement

Given the roots of two binary trees `p` and `q`, write a function to check if they are the same — structurally identical with the same node values.

```
Input:  p = [1,2,3], q = [1,2,3]
Output: true

Input:  p = [1,2], q = [1,null,2]
Output: false
```

### Intuition

Two trees are the same if:
1. Both are `None` → True
2. One is `None` and the other isn't → False
3. Values at current nodes differ → False
4. Left subtrees are the same AND right subtrees are the same → True

### Solution

```python
def isSameTree(p, q) -> bool:
    if not p and not q:
        return True
    if not p or not q:
        return False
    if p.val != q.val:
        return False
    return isSameTree(p.left, q.left) and isSameTree(p.right, q.right)
```

**Time:** O(n) | **Space:** O(h)

### Interview Traps

**Trap 1 — Checking only values, not structure**
`[1,2]` and `[1,null,2]` have the same values but different structures → not the same tree.  
✅ Check both structure (None checks) and values.

**Trap 2 — Order of null checks**
Check both-null first, then one-null, then compare values.

**Trap 3 — Iterative approach**
Use a stack or queue with paired node comparisons if iterative is required.
```python
from collections import deque
queue = deque([(p, q)])
while queue:
    n1, n2 = queue.popleft()
    if not n1 and not n2: continue
    if not n1 or not n2 or n1.val != n2.val: return False
    queue.append((n1.left, n2.left))
    queue.append((n1.right, n2.right))
return True
```

---

## 51. Subtree of Another Tree

**LeetCode #572 | Difficulty: 🟢 Easy**

### Problem Statement

Given the roots of two binary trees `root` and `subRoot`, return `true` if there is a subtree of `root` with the same structure and node values as `subRoot`.

```
Input:  root = [3,4,5,1,2], subRoot = [4,1,2]
Output: true
```

### Intuition

For each node in `root`, check if the subtree rooted at that node is the same as `subRoot` using the `isSameTree` function. If any node matches → return `True`.

### Solution

```python
def isSubtree(root, subRoot) -> bool:
    if not subRoot:
        return True
    if not root:
        return False
    if isSameTree(root, subRoot):
        return True
    return isSubtree(root.left, subRoot) or isSubtree(root.right, subRoot)

def isSameTree(p, q) -> bool:
    if not p and not q:
        return True
    if not p or not q or p.val != q.val:
        return False
    return isSameTree(p.left, q.left) and isSameTree(p.right, q.right)
```

**Time:** O(m·n) where m, n are the tree sizes | **Space:** O(h)

### Interview Traps

**Trap 1 — Checking values without verifying full structure**
Node values alone don't identify a subtree match — the entire subtree must match.  
✅ Always use `isSameTree` for the subtree comparison.

**Trap 2 — "Can you do it in O(m+n)?"**
Yes — serialize both trees using pre-order with null markers and use string matching (KMP). This is an advanced follow-up; know it exists.

**Trap 3 — subRoot is None**
An empty tree is a subtree of any tree → return `True` immediately.

**Trap 4 — Confusing subtree with subgraph**
A subtree must include all descendants of the matched node — it can't be a partial match.

---

## 52. Lowest Common Ancestor of a BST

**LeetCode #235 | Difficulty: 🟡 Medium**

### Problem Statement

Given a BST and two nodes `p` and `q`, find their lowest common ancestor (LCA) — the deepest node that has both `p` and `q` as descendants (a node can be a descendant of itself).

```
Input:  root = [6,2,8,0,4,7,9], p = 2, q = 8
Output: 6

Input:  root = [6,2,8,0,4,7,9], p = 2, q = 4
Output: 2
```

### Intuition

Leverage the BST property:
- If both `p` and `q` are less than `root.val` → LCA is in the left subtree.
- If both are greater → LCA is in the right subtree.
- Otherwise (they straddle root, or one equals root) → current node is the LCA.

### Solution

```python
def lowestCommonAncestor(root, p, q):
    while root:
        if p.val < root.val and q.val < root.val:
            root = root.left
        elif p.val > root.val and q.val > root.val:
            root = root.right
        else:
            return root
```

**Time:** O(h) | **Space:** O(1) iterative

### Interview Traps

**Trap 1 — Using the general binary tree LCA algorithm**
The general LCA (LeetCode #236) uses post-order DFS and is O(n). For a BST, the BST property gives us O(h) with no extra space.  
✅ Always exploit BST ordering when the problem specifies a BST.

**Trap 2 — Not handling the case where p or q equals root**
If `p.val == root.val` or `q.val == root.val`, the current node is the LCA. The `else` branch handles this correctly.

**Trap 3 — Recursive vs iterative**
Both work for BST LCA. The iterative version is O(1) space — prefer it to show optimization awareness.

**Trap 4 — "What if it's not a BST?" (LeetCode #236)**
Then you need the recursive post-order approach:
```python
def lca(root, p, q):
    if not root or root == p or root == q:
        return root
    left = lca(root.left, p, q)
    right = lca(root.right, p, q)
    return root if left and right else left or right
```

---

## 53. Binary Tree Level Order Traversal

**LeetCode #102 | Difficulty: 🟡 Medium**

### Problem Statement

Given the root of a binary tree, return the level order traversal of its nodes' values (i.e., left to right, level by level).

```
Input:  [3,9,20,null,null,15,7]
Output: [[3],[9,20],[15,7]]
```

### Intuition

BFS with a queue. Process all nodes at the current level before moving to the next. Use `len(queue)` at the start of each level to know how many nodes belong to that level.

### Solution

```python
from collections import deque

def levelOrder(root) -> list[list[int]]:
    if not root:
        return []
    result = []
    queue = deque([root])

    while queue:
        level = []
        for _ in range(len(queue)):
            node = queue.popleft()
            level.append(node.val)
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
        result.append(level)

    return result
```

**Time:** O(n) | **Space:** O(n)

### Interview Traps

**Trap 1 — Not grouping nodes by level**
Simply doing BFS and collecting all nodes gives the right order but not the right grouping.  
✅ Use `for _ in range(len(queue))` to process exactly one level at a time.

**Trap 2 — Using DFS for level order**
DFS can simulate BFS using depth tracking, but it's unnatural and error-prone for this problem.  
✅ BFS is the canonical approach.

**Trap 3 — "Reverse level order" follow-up (LeetCode #107)**
Simply reverse the result list at the end: `return result[::-1]`.

**Trap 4 — "Zigzag level order" follow-up (LeetCode #103)**
Alternate the direction of appending at each level using a flag.

---

## 54. Binary Tree Right Side View

**LeetCode #199 | Difficulty: 🟡 Medium**

### Problem Statement

Given the root of a binary tree, imagine yourself standing on the right side of it. Return the values of the nodes you can see (the rightmost node at each level).

```
Input:  [1,2,3,null,5,null,4]
Output: [1,3,4]
```

### Intuition

BFS level order traversal — at each level, take only the **last** node's value. Alternatively, DFS (right subtree first) — the first node seen at each depth is the rightmost.

### Solution

```python
from collections import deque

def rightSideView(root) -> list[int]:
    if not root:
        return []
    result = []
    queue = deque([root])

    while queue:
        for i in range(len(queue)):
            node = queue.popleft()
            if i == len(queue):  # last node at this level
                result.append(node.val)
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
    return result
```

**Cleaner BFS version:**
```python
from collections import deque

def rightSideView(root) -> list[int]:
    result = []
    queue = deque([root] if root else [])
    while queue:
        level_size = len(queue)
        for i in range(level_size):
            node = queue.popleft()
            if i == level_size - 1:
                result.append(node.val)
            if node.left: queue.append(node.left)
            if node.right: queue.append(node.right)
    return result
```

**Time:** O(n) | **Space:** O(n)

### Interview Traps

**Trap 1 — Taking the rightmost child instead of rightmost visible node**
A node might be visible even if its parent has no right child — it just needs to be the last at its level.

**Trap 2 — "Left side view" follow-up**
Take the **first** node at each level instead of the last.

**Trap 3 — DFS approach**
```python
def rightSideView(root):
    result = []
    def dfs(node, depth):
        if not node: return
        if depth == len(result):
            result.append(node.val)
        dfs(node.right, depth + 1)
        dfs(node.left, depth + 1)
    dfs(root, 0)
    return result
```
✅ Visit right before left — the first node seen at each depth is the rightmost.

---

## 55. Count Good Nodes in Binary Tree

**LeetCode #1448 | Difficulty: 🟡 Medium**

### Problem Statement

Given a binary tree, a node `X` is **good** if, on the path from root to `X`, there is no node with a value greater than `X`. Return the number of good nodes.

```
Input:  [3,1,4,3,null,1,5]
Output: 4  (nodes 3, 4, 3, 5)
```

### Intuition

DFS, tracking the maximum value seen on the path from root to the current node. A node is "good" if its value ≥ the running max. Update the max as you go deeper.

### Solution

```python
def goodNodes(root) -> int:
    def dfs(node, max_so_far):
        if not node:
            return 0
        good = 1 if node.val >= max_so_far else 0
        new_max = max(max_so_far, node.val)
        return good + dfs(node.left, new_max) + dfs(node.right, new_max)

    return dfs(root, float('-inf'))
```

**Time:** O(n) | **Space:** O(h)

### Interview Traps

**Trap 1 — Using a global max instead of path max**
The global max of the tree is irrelevant — only the max on the path from root to the current node matters.  
✅ Pass `max_so_far` as a parameter, not a global.

**Trap 2 — Not counting root**
The root is always a good node (no ancestors). Initializing `max_so_far = float('-inf')` ensures the root is always counted.

**Trap 3 — Counting bad nodes instead of good**
Read carefully — good nodes have `node.val >= max_so_far`, not `>`.

---

## 56. Validate Binary Search Tree

**LeetCode #98 | Difficulty: 🟡 Medium**

### Problem Statement

Given the root of a binary tree, determine if it is a valid BST. A valid BST has every node in the left subtree strictly less than the current node, and every node in the right subtree strictly greater.

```
Input:  [2,1,3]
Output: true

Input:  [5,1,4,null,null,3,6]
Output: false
```

### Intuition

Pass down a valid range `(min_val, max_val)` for each node. At every node, check if `min_val < node.val < max_val`. Recurse left with updated upper bound, recurse right with updated lower bound.

### Solution

```python
def isValidBST(root) -> bool:
    def validate(node, min_val, max_val):
        if not node:
            return True
        if not (min_val < node.val < max_val):
            return False
        return (validate(node.left, min_val, node.val) and
                validate(node.right, node.val, max_val))

    return validate(root, float('-inf'), float('inf'))
```

**Time:** O(n) | **Space:** O(h)

### Interview Traps

**Trap 1 — Only checking immediate children**
`[10, 5, 15, null, null, 6, 20]` — node 6 is in the right subtree of 10 but less than 10. Checking only parent-child pairs misses this.  
✅ Pass the valid range down through the entire tree.

**Trap 2 — Using ≤ instead of <**
A valid BST requires **strict** inequality — equal values are not allowed.

**Trap 3 — In-order traversal approach**
An in-order traversal of a BST produces a strictly increasing sequence. Check that each value is greater than the previous.
```python
def isValidBST(root):
    prev = float('-inf')
    def inorder(node):
        nonlocal prev
        if not node: return True
        if not inorder(node.left): return False
        if node.val <= prev: return False
        prev = node.val
        return inorder(node.right)
    return inorder(root)
```

---

## 57. Kth Smallest Element in a BST

**LeetCode #230 | Difficulty: 🟡 Medium**

### Problem Statement

Given the root of a BST and an integer `k`, return the kth smallest value in the BST.

```
Input:  root = [3,1,4,null,2], k = 1
Output: 1
```

### Intuition

In-order traversal of a BST visits nodes in sorted (ascending) order. The kth node visited during in-order traversal is the kth smallest.

### Solution

```python
def kthSmallest(root, k: int) -> int:
    # Iterative in-order traversal
    stack = []
    curr = root
    count = 0

    while curr or stack:
        while curr:
            stack.append(curr)
            curr = curr.left
        curr = stack.pop()
        count += 1
        if count == k:
            return curr.val
        curr = curr.right

    return -1  # k is guaranteed valid
```

**Time:** O(h + k) | **Space:** O(h)

### Interview Traps

**Trap 1 — Collecting all values then indexing**
Collecting all n values and returning the kth is O(n) time and space — wasteful.  
✅ Stop as soon as you've seen k elements.

**Trap 2 — Recursive approach forgetting to stop early**
Recursive in-order can early-exit using a counter and exception or nonlocal variable.

**Trap 3 — "What if the BST is frequently modified?" (follow-up)**
Augment each node with the size of its left subtree to find the kth smallest in O(h) without traversal. This is a common system design follow-up.

**Trap 4 — Kth largest instead of smallest**
For kth largest, do reverse in-order (right → root → left).

---

## 58. Construct Binary Tree from Preorder and Inorder Traversal

**LeetCode #105 | Difficulty: 🟡 Medium**

### Problem Statement

Given arrays `preorder` and `inorder` representing the traversals of a binary tree, construct and return the binary tree.

```
Input:  preorder = [3,9,20,15,7], inorder = [9,3,15,20,7]
Output: [3,9,20,null,null,15,7]
```

### Intuition

- The **first element** of `preorder` is always the root.
- Find the root's position in `inorder` — elements to its left are the left subtree, elements to its right are the right subtree.
- Recursively build both subtrees.

Use a HashMap for O(1) index lookup in `inorder`.

### Solution

```python
def buildTree(preorder: list[int], inorder: list[int]):
    inorder_idx = {val: i for i, val in enumerate(inorder)}
    pre_idx = [0]  # mutable pointer

    def build(left, right):
        if left > right:
            return None
        root_val = preorder[pre_idx[0]]
        pre_idx[0] += 1
        root = TreeNode(root_val)
        mid = inorder_idx[root_val]
        root.left = build(left, mid - 1)
        root.right = build(mid + 1, right)
        return root

    return build(0, len(inorder) - 1)
```

**Time:** O(n) | **Space:** O(n)

### Interview Traps

**Trap 1 — Linear search in inorder instead of HashMap**
Finding the root in `inorder` with linear search is O(n) per call → O(n²) total.  
✅ Pre-build a HashMap: `inorder_idx = {val: idx}`.

**Trap 2 — Slicing arrays instead of using indices**
Creating subarrays via slicing is O(n) per call — use left/right index bounds instead.

**Trap 3 — "What about postorder and inorder?" (LeetCode #106)**
The last element of `postorder` is the root. Logic is symmetric — build right subtree before left.

**Trap 4 — Unique values assumption**
The problem guarantees unique values — the HashMap approach relies on this.

---

## 59. Binary Tree Maximum Path Sum

**LeetCode #124 | Difficulty: 🔴 Hard**

### Problem Statement

A path in a binary tree is any sequence of nodes where each pair of adjacent nodes has an edge between them. Return the maximum path sum (the path must contain at least one node).

```
Input:  [-10,9,20,null,null,15,7]
Output: 42  (path: 15→20→7)
```

### Intuition

For each node, the best path **passing through** it = `node.val + max(0, left_gain) + max(0, right_gain)`. The path **contribution upward** = `node.val + max(0, max(left_gain, right_gain))` — you can only extend in one direction upward.

Track the global maximum across all nodes.

### Solution

```python
def maxPathSum(root) -> int:
    max_sum = [float('-inf')]

    def dfs(node):
        if not node:
            return 0
        left_gain = max(0, dfs(node.left))
        right_gain = max(0, dfs(node.right))
        # Best path through this node
        max_sum[0] = max(max_sum[0], node.val + left_gain + right_gain)
        # Return max single-arm contribution upward
        return node.val + max(left_gain, right_gain)

    dfs(root)
    return max_sum[0]
```

**Time:** O(n) | **Space:** O(h)

### Interview Traps

**Trap 1 — Allowing negative contributions**
If `left_gain` is negative, it reduces the path sum. Use `max(0, gain)` to optionally exclude either subtree.

**Trap 2 — Returning both arms upward**
A path can only travel in one direction through a node — you can't split upward. Return only the better arm: `node.val + max(left_gain, right_gain)`.

**Trap 3 — All-negative tree**
Initialize `max_sum = float('-inf')`, not 0 — the path must include at least one node, and that node might be negative.

**Trap 4 — Confusing path with subtree**
A path doesn't have to go root-to-leaf — it can be any connected sequence of nodes.

---

## 60. Serialize and Deserialize Binary Tree

**LeetCode #297 | Difficulty: 🔴 Hard**

### Problem Statement

Design an algorithm to serialize a binary tree to a string and deserialize it back. There is no restriction on your serialization format.

```
Input:  root = [1,2,3,null,null,4,5]
Serialize:   "1,2,N,N,3,4,N,N,5,N,N"
Deserialize: [1,2,3,null,null,4,5]
```

### Intuition

Use **pre-order DFS** with `"N"` as a null marker. During serialization, append each node value and `"N"` for nulls. During deserialization, use a queue of tokens — consume the next token to build each node recursively.

### Solution

```python
from collections import deque

class Codec:
    def serialize(self, root) -> str:
        result = []
        def dfs(node):
            if not node:
                result.append("N")
                return
            result.append(str(node.val))
            dfs(node.left)
            dfs(node.right)
        dfs(root)
        return ",".join(result)

    def deserialize(self, data: str):
        tokens = deque(data.split(","))
        def dfs():
            val = tokens.popleft()
            if val == "N":
                return None
            node = TreeNode(int(val))
            node.left = dfs()
            node.right = dfs()
            return node
        return dfs()
```

**Time:** O(n) | **Space:** O(n)

### Interview Traps

**Trap 1 — Not encoding null nodes**
Without null markers, you can't distinguish different tree structures with the same pre-order sequence.  
✅ Always include null markers — they're what make deserialization unambiguous.

**Trap 2 — Using BFS serialization**
BFS (level-order) also works but requires more careful null handling. Pre-order DFS is cleaner and recursive.

**Trap 3 — Using an index instead of a deque for deserialization**
A shared mutable index (`idx = [0]`) or `deque` with `popleft()` both work. The deque is more intuitive.

**Trap 4 — Separator collision**
If node values can contain commas, use a different separator. The problem guarantees integer values, so `","` is safe.

**Trap 5 — Empty tree**
`serialize(None)` → `"N"`. `deserialize("N")` → `None`. Handle this correctly.

---

*[← Back to Index](./index.md) | [Next: Tries →](./08_tries.md)*

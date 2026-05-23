# Tries

**Topic 8 of 18 | NeetCode 150 Interview Handbook**  
[← Back to Index](./index.md)

---

## Problems in this Section

| # | Problem | Difficulty |
|---|---------|------------|
| 61 | [Implement Trie (Prefix Tree)](#61-implement-trie-prefix-tree) | 🟡 Medium |
| 62 | [Design Add and Search Words Data Structure](#62-design-add-and-search-words-data-structure) | 🟡 Medium |
| 63 | [Word Search II](#63-word-search-ii) | 🔴 Hard |

---

## 61. Implement Trie (Prefix Tree)

**LeetCode #208 | Difficulty: 🟡 Medium**

### Problem Statement

Implement a Trie with `insert`, `search`, and `startsWith` methods.

```
trie.insert("apple")
trie.search("apple")   → true
trie.search("app")     → false
trie.startsWith("app") → true
trie.insert("app")
trie.search("app")     → true
```

### Intuition

A Trie is a tree where each node represents a character. Each node has up to 26 children (for lowercase letters) and a boolean flag marking if a complete word ends there.

!!! info "Trie Visual"
    ```mermaid
    graph TD
        Root(( )) --> a((a))
        a --> p1((p))
        p1 --> p2((p))
        style p2 fill:#f96
        Root --> b((b))
        b --> a2((a))
        a2 --> d((d))
        style d fill:#f96
    ```
    *Orange nodes mark the end of words (e.g., "app", "bad").*

- `insert`: Walk the trie character by character, creating nodes as needed. Mark the final node as a word end.
- `search`: Walk and return `True` only if the path exists AND the final node is marked as a word end.
- `startsWith`: Walk and return `True` if the path exists (don't need word-end marker).

### Solution

```python
class TrieNode:
    def __init__(self):
        self.children = {}
        self.is_end = False

class Trie:
    def __init__(self):
        self.root = TrieNode()

    def insert(self, word: str) -> None:
        node = self.root
        for char in word:
            if char not in node.children:
                node.children[char] = TrieNode()
            node = node.children[char]
        node.is_end = True

    def search(self, word: str) -> bool:
        node = self.root
        for char in word:
            if char not in node.children:
                return False
            node = node.children[char]
        return node.is_end

    def startsWith(self, prefix: str) -> bool:
        node = self.root
        for char in prefix:
            if char not in node.children:
                return False
            node = node.children[char]
        return True
```

**Time:** O(m) per operation where m = word length | **Space:** O(n·m) total

### Interview Traps

**Trap 1 — Confusing `search` and `startsWith`**
`search` requires `is_end = True`; `startsWith` does not.  
✅ Both traverse the same path — only the final check differs.

**Trap 2 — Using a list of 26 instead of a dict**
A `[None] * 26` array works but wastes space for sparse tries.  
✅ A `dict` is more space-efficient and handles non-lowercase input.

**Trap 3 — Forgetting to mark `is_end`**
Without `is_end`, you can't distinguish "app" (inserted) from "apple" (inserted) when searching for "app".

**Trap 4 — "What's the time/space trade-off vs a HashSet?"**
A HashSet for `search` is O(1) lookup but can't do prefix queries. A Trie enables O(m) prefix search — that's its core advantage.

**Trap 5 — delete operation follow-up**
Deletion requires checking if a node has other children before removing. Mark `is_end = False` first; only remove nodes if they have no children and are not word ends.

---

## 62. Design Add and Search Words Data Structure

**LeetCode #211 | Difficulty: 🟡 Medium**

### Problem Statement

Design a data structure that supports adding new words and searching if a string matches any previously added string. The search may contain `'.'` as a wildcard that matches any character.

```
wordDictionary.addWord("bad")
wordDictionary.addWord("dad")
wordDictionary.addWord("mad")
wordDictionary.search("pad") → false
wordDictionary.search("bad") → true
wordDictionary.search(".ad") → true
wordDictionary.search("b..") → true
```

### Intuition

Use a Trie for storage. For `search`, if the current character is `'.'`, try all 26 possible children recursively. If it's a regular character, follow the normal Trie path.

### Solution

```python
class TrieNode:
    def __init__(self):
        self.children = {}
        self.is_end = False

class WordDictionary:
    def __init__(self):
        self.root = TrieNode()

    def addWord(self, word: str) -> None:
        node = self.root
        for char in word:
            if char not in node.children:
                node.children[char] = TrieNode()
            node = node.children[char]
        node.is_end = True

    def search(self, word: str) -> bool:
        def dfs(node, i):
            if i == len(word):
                return node.is_end
            char = word[i]
            if char == '.':
                for child in node.children.values():
                    if dfs(child, i + 1):
                        return True
                return False
            if char not in node.children:
                return False
            return dfs(node.children[char], i + 1)

        return dfs(self.root, 0)
```

**Time:** O(m) average, O(26^m) worst case with all wildcards | **Space:** O(n·m)

### Interview Traps

**Trap 1 — Using a list and linear search**
Checking every added word against the pattern is O(n·m) per query.  
✅ The Trie prunes branches that can't match — far more efficient in practice.

**Trap 2 — Iterative approach with wildcards**
Wildcards require branching — recursion or an explicit stack of (node, index) pairs.

**Trap 3 — "What if patterns have consecutive dots?"**
The DFS handles this naturally — each `'.'` branches into all children independently.

**Trap 4 — Worst case with many wildcards**
`search("...")` tries all paths of length 3 → O(26^3). In practice, this is bounded since the trie prunes non-existent paths.

---

## 63. Word Search II

**LeetCode #212 | Difficulty: 🔴 Hard**

### Problem Statement

Given an `m x n` board of characters and a list of words, return all words in the list that can be found in the board. Words can be constructed from adjacent (horizontally or vertically) cells, and each cell may not be used more than once per word.

```
Input:  board = [["o","a","a","n"],
                 ["e","t","a","e"],
                 ["i","h","k","r"],
                 ["i","f","l","v"]],
        words = ["oath","pea","eat","rain"]
Output: ["eat","oath"]
```

### Intuition

Build a **Trie** from all the words. Then run DFS from every cell on the board. At each step, only continue if the current path exists in the Trie — this prunes invalid paths early. When a complete word is found, add it to results and mark it as found to avoid duplicates.

### Solution

```python
class TrieNode:
    def __init__(self):
        self.children = {}
        self.word = None  # stores the complete word at end nodes

class Solution:
    def findWords(self, board: list[list[str]], words: list[str]) -> list[str]:
        # Build trie
        root = TrieNode()
        for word in words:
            node = root
            for char in word:
                if char not in node.children:
                    node.children[char] = TrieNode()
                node = node.children[char]
            node.word = word

        rows, cols = len(board), len(board[0])
        result = []

        def dfs(node, r, c):
            char = board[r][c]
            if char not in node.children:
                return
            next_node = node.children[char]
            if next_node.word:
                result.append(next_node.word)
                next_node.word = None  # avoid duplicates

            board[r][c] = '#'  # mark visited
            for dr, dc in [(0,1),(0,-1),(1,0),(-1,0)]:
                nr, nc = r + dr, c + dc
                if 0 <= nr < rows and 0 <= nc < cols and board[nr][nc] != '#':
                    dfs(next_node, nr, nc)
            board[r][c] = char  # restore

            # Prune: remove leaf nodes to speed up future searches
            if not next_node.children:
                del node.children[char]

        for r in range(rows):
            for c in range(cols):
                dfs(root, r, c)

        return result
```

**Time:** O(m·n·4·3^(L-1)) where L = max word length | **Space:** O(total chars in words)

### Interview Traps

**Trap 1 — Running DFS for each word separately**
DFS per word is O(words × m × n × 4^L) — extremely slow.  
✅ Build a Trie once and run a single DFS pass that checks all words simultaneously.

**Trap 2 — Not pruning visited cells**
Mark visited cells with a sentinel (e.g., `'#'`) during DFS and restore afterward.

**Trap 3 — Duplicate results**
The same word could be found via different paths. Set `node.word = None` after adding to result.

**Trap 4 — Not pruning empty Trie nodes**
After a word is found, prune leaf nodes (`del node.children[char]`) to speed up future DFS calls — a key optimization.

**Trap 5 — "Why Trie instead of a HashSet of words?"**
A HashSet requires completing a full DFS path before checking. A Trie prunes the search at every character, dramatically reducing work.

---

*[← Back to Index](./index.md) | [Next: Heap / Priority Queue →](./09_heap.md)*

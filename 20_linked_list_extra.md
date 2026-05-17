# Linked List — Extra Problems

**Grind 169 Supplement | Not in NeetCode 150**  
[← Back to Index](./index.md)

---

## Problems in this Section

| # | Problem | Difficulty |
|---|---------|------------|
| 1 | [Middle of the Linked List](#1-middle-of-the-linked-list) | 🟢 Easy |
| 2 | [Palindrome Linked List](#2-palindrome-linked-list) | 🟢 Easy |
| 3 | [Swap Nodes in Pairs](#3-swap-nodes-in-pairs) | 🟡 Medium |
| 4 | [Odd Even Linked List](#4-odd-even-linked-list) | 🟡 Medium |
| 5 | [Sort List](#5-sort-list) | 🟡 Medium |
| 6 | [Rotate List](#6-rotate-list) | 🟡 Medium |

---

## 1. Middle of the Linked List

**LeetCode #876 | Difficulty: 🟢 Easy**

### Problem Statement

Given the head of a singly linked list, return the middle node. If there are two middle nodes, return the second one.

```
Input:  1 → 2 → 3 → 4 → 5
Output: node 3

Input:  1 → 2 → 3 → 4 → 5 → 6
Output: node 4  (second middle)
```

### Intuition

Classic **slow/fast pointer** (Floyd's tortoise and hare). Move `slow` one step and `fast` two steps at a time. When `fast` reaches the end, `slow` is at the middle.

For even-length lists, `slow` naturally lands on the second middle node.

### Solution

```python
def middleNode(head):
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    return slow
```

**Time:** O(n) | **Space:** O(1)

### Interview Traps

**Trap 1 — Two-pass approach**
Count length first, then traverse to `n//2`. Works but is O(n) two passes vs O(n) one pass.  
✅ Slow/fast pointer is the elegant one-pass solution.

**Trap 2 — First vs second middle for even length**
`[1,2,3,4]` has middles at 2 and 3. The problem asks for the second. The fast/slow pattern naturally returns the second — verify this.

**Trap 3 — Single-node list**
`fast` starts at `head`, `fast.next` is `None` → loop never runs → returns `head`. Correct.

---

## 2. Palindrome Linked List

**LeetCode #234 | Difficulty: 🟢 Easy**

### Problem Statement

Given the head of a singly linked list, return `true` if it is a palindrome.

```
Input:  1 → 2 → 2 → 1
Output: true

Input:  1 → 2
Output: false
```

### Intuition

Three steps:
1. Find the middle using slow/fast pointers.
2. Reverse the second half in-place.
3. Compare first half and reversed second half node by node.
4. (Optional) Restore the list.

### Solution

```python
def isPalindrome(head) -> bool:
    # Step 1: Find middle
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next

    # Step 2: Reverse second half
    prev, curr = None, slow
    while curr:
        nxt = curr.next
        curr.next = prev
        prev = curr
        curr = nxt
    second_half = prev

    # Step 3: Compare
    p1, p2 = head, second_half
    while p2:
        if p1.val != p2.val:
            return False
        p1 = p1.next
        p2 = p2.next

    return True
```

**Time:** O(n) | **Space:** O(1)

### Interview Traps

**Trap 1 — Using extra space (converting to array)**
```python
vals = []
while head:
    vals.append(head.val)
    head = head.next
return vals == vals[::-1]
```
Valid but O(n) space. The in-place reversal approach is O(1).

**Trap 2 — Not restoring the list**
Reversing the second half modifies the original list. In production, restore it after checking. Mention this to the interviewer.

**Trap 3 — Odd-length lists**
For `1→2→3→2→1`, slow stops at `3` (middle). Reversing from `3` onward gives `1←2←3` and `2→1`. Comparing `1→2` with `1→2→3` — the `p2` loop ends when `p2` is None, so the middle element is correctly skipped.

---

## 3. Swap Nodes in Pairs

**LeetCode #24 | Difficulty: 🟡 Medium**

### Problem Statement

Given a linked list, swap every two adjacent nodes and return its head. You must solve it without modifying the values of the nodes.

```
Input:  1 → 2 → 3 → 4
Output: 2 → 1 → 4 → 3

Input:  1 → 2 → 3
Output: 2 → 1 → 3
```

### Intuition

Use a dummy head. For each pair, rewire: `dummy → second → first → (next pair)`. Advance the pointer by 2 nodes per iteration.

### Solution

```python
def swapPairs(head):
    dummy = ListNode(0, head)
    prev = dummy

    while prev.next and prev.next.next:
        first = prev.next
        second = prev.next.next

        # Rewire
        prev.next = second
        first.next = second.next
        second.next = first

        # Advance
        prev = first

    return dummy.next
```

**Recursive version:**
```python
def swapPairs(head):
    if not head or not head.next:
        return head
    second = head.next
    head.next = swapPairs(second.next)
    second.next = head
    return second
```

**Time:** O(n) | **Space:** O(1) iterative, O(n) recursive

### Interview Traps

**Trap 1 — Losing the next pair reference**
Save `second.next` before rewiring — it becomes the starting point of the next pair.

**Trap 2 — Not using a dummy head**
Without a dummy, the new head (second node) requires special-case handling.

**Trap 3 — Advancing prev incorrectly**
After swapping, `prev` should point to `first` (the node now in second position), not `second`.

**Trap 4 — Modifying values instead of pointers**
The problem explicitly says not to modify node values. Swap the pointer structure only.

---

## 4. Odd Even Linked List

**LeetCode #328 | Difficulty: 🟡 Medium**

### Problem Statement

Given the head of a singly linked list, group all odd-indexed nodes together followed by even-indexed nodes. The first node is considered odd (1-indexed).

```
Input:  1 → 2 → 3 → 4 → 5
Output: 1 → 3 → 5 → 2 → 4

Input:  2 → 1 → 3 → 5 → 6 → 4 → 7
Output: 2 → 3 → 6 → 7 → 1 → 5 → 4
```

### Intuition

Maintain two separate chains: one for odd-indexed nodes, one for even-indexed nodes. Weave through the list alternately connecting to each chain. Finally, connect the tail of the odd chain to the head of the even chain.

### Solution

```python
def oddEvenList(head):
    if not head:
        return head

    odd = head
    even = head.next
    even_head = even

    while even and even.next:
        odd.next = even.next   # connect odd to next odd
        odd = odd.next
        even.next = odd.next   # connect even to next even
        even = even.next

    odd.next = even_head  # join odd tail to even head
    return head
```

**Time:** O(n) | **Space:** O(1)

### Interview Traps

**Trap 1 — Confusing index-based (position) with value-based (odd/even value)**
The problem groups by **position** (1st, 3rd, 5th nodes), not by whether the node's **value** is odd or even.

**Trap 2 — Not saving even_head before the loop**
Once the loop starts, `even` moves forward. Save the original `even` head to attach at the end.

**Trap 3 — Loop condition**
Check `even and even.next` — if `even` is null (even-length list ended) or `even.next` is null (no more odd nodes), stop.

---

## 5. Sort List

**LeetCode #148 | Difficulty: 🟡 Medium**

### Problem Statement

Given the head of a linked list, return the list sorted in ascending order in O(n log n) time and O(1) space.

```
Input:  4 → 2 → 1 → 3
Output: 1 → 2 → 3 → 4

Input:  -1 → 5 → 3 → 4 → 0
Output: -1 → 0 → 3 → 4 → 5
```

### Intuition

**Merge sort** on a linked list. Unlike arrays, linked lists support O(1) splitting at the midpoint (using slow/fast pointers) and O(1) merging (no extra array needed). This achieves O(n log n) time and O(log n) space for recursion stack (O(1) if done bottom-up iteratively).

### Solution

```python
def sortList(head):
    if not head or not head.next:
        return head

    # Step 1: Find middle and split
    slow, fast = head, head.next
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    mid = slow.next
    slow.next = None  # cut the list

    # Step 2: Recursively sort both halves
    left = sortList(head)
    right = sortList(mid)

    # Step 3: Merge
    dummy = ListNode(0)
    curr = dummy
    while left and right:
        if left.val <= right.val:
            curr.next = left
            left = left.next
        else:
            curr.next = right
            right = right.next
        curr = curr.next
    curr.next = left or right

    return dummy.next
```

**Time:** O(n log n) | **Space:** O(log n) recursion stack

### Interview Traps

**Trap 1 — Using quicksort (O(n²) worst case)**
Quicksort on a linked list doesn't have O(1) random access for pivot selection — merge sort is the standard choice.

**Trap 2 — Not cutting the list at mid**
`slow.next = None` splits the list into two halves. Without this, both recursive calls get the full list.

**Trap 3 — Finding middle with `fast = head` (wrong for split)**
Using `fast = head` makes `slow` stop one node too late for odd-length lists in some implementations. Using `fast = head.next` ensures the left half is the longer one for odd-length lists.

**Trap 4 — O(1) space bottom-up merge sort (follow-up)**
Merge pairs of size 1, then 2, then 4, etc. without recursion. Complex to implement but achieves true O(1) space.

---

## 6. Rotate List

**LeetCode #61 | Difficulty: 🟡 Medium**

### Problem Statement

Given the head of a linked list, rotate the list to the right by `k` places.

```
Input:  1 → 2 → 3 → 4 → 5, k = 2
Output: 4 → 5 → 1 → 2 → 3

Input:  0 → 1 → 2, k = 4
Output: 2 → 0 → 1
```

### Intuition

1. Find the length `n` and make the list **circular** by connecting tail to head.
2. The new tail is at position `n - (k % n) - 1` from the original head.
3. The new head is at position `n - (k % n)`.
4. Break the circular link at the new tail.

### Solution

```python
def rotateRight(head, k: int):
    if not head or not head.next or k == 0:
        return head

    # Find length and tail
    tail = head
    n = 1
    while tail.next:
        tail = tail.next
        n += 1

    k %= n
    if k == 0:
        return head

    # Make circular
    tail.next = head

    # Find new tail (n - k steps from original head)
    new_tail = head
    for _ in range(n - k - 1):
        new_tail = new_tail.next

    new_head = new_tail.next
    new_tail.next = None  # break the circle

    return new_head
```

**Time:** O(n) | **Space:** O(1)

### Interview Traps

**Trap 1 — Not handling k > n**
`k = 7` on a list of length 5 is the same as `k = 2`. Always `k %= n` first.

**Trap 2 — Not making the list circular**
The circular approach is the cleanest. Without it, you need to manually track both ends.

**Trap 3 — Off-by-one in new tail position**
New tail is at index `n - k - 1` (0-indexed). Walk `n - k - 1` steps from head.

**Trap 4 — Forgetting to break the circle**
Always set `new_tail.next = None` — otherwise you return a circular list.

---

*[← Back to Index](./index.md) | [Next: Trees Extra →](./21_trees_extra.md)*

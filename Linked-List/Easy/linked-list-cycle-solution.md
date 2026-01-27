# Linked List Cycle

## Problem Information
- **Difficulty**: Easy
- **Category**: Linked-List
- **Tags**: Linked List, Two Pointers, Hash Table
- **LeetCode Link**: https://leetcode.com/problems/linked-list-cycle/

## Problem Statement

Given `head`, the head of a linked list, determine if the linked list has a cycle in it.

There is a cycle in a linked list if there is some node in the list that can be reached again by continuously following the `next` pointer. Internally, `pos` is used to denote the index of the node that tail's `next` pointer is connected to. **Note that `pos` is not passed as a parameter.**

Return `true` if there is a cycle in the linked list. Otherwise, return `false`.

### Examples

**Example 1:**
```
Input: head = [3,2,0,-4], pos = 1
Output: true
Explanation: There is a cycle in the linked list, where the tail connects to the 1st node (0-indexed).
```

**Example 2:**
```
Input: head = [1,2], pos = 0
Output: true
Explanation: There is a cycle in the linked list, where the tail connects to the 0th node.
```

**Example 3:**
```
Input: head = [1], pos = -1
Output: false
Explanation: There is no cycle in the linked list.
```

### Constraints

- The number of nodes in the list is in the range `[0, 10^4]`.
- `-10^5 <= Node.val <= 10^5`
- `pos` is `-1` or a valid index in the linked list.

**Follow up:** Can you solve it using O(1) (i.e. constant) memory?

## Core Concept / Pattern

**Floyd's Cycle Detection Algorithm** (Tortoise and Hare): Use two pointers moving at different speeds. If there is a cycle, the fast pointer will eventually meet the slow pointer. If there is no cycle, the fast pointer will reach the end (null).

## Approach 1: Hash Set

### Intuition

Keep track of every node we have visited. If we encounter a node we have already seen, there is a cycle. If we reach null, there is no cycle.

### Algorithm

1. Initialize an empty set `visited`.
2. Traverse the list. For each node:
   - If the node is already in `visited`, return `True`.
   - Otherwise, add the node to `visited`.
3. If we reach `None`, return `False`.

### Implementation

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def hasCycle(self, head: Optional[ListNode]) -> bool:
        visited = set()
        curr = head

        while curr:
            if curr in visited:
                return True
            visited.add(curr)
            curr = curr.next

        return False
```

### Complexity Analysis
- **Time Complexity**: O(n) -- each node is visited at most once before a cycle is detected or the end is reached.
- **Space Complexity**: O(n) -- the set stores up to n node references.

### Why We Can Do Better

The hash set uses O(n) extra space. The follow-up asks for O(1) space. Floyd's algorithm achieves this.

## Approach 2: Floyd's Tortoise and Hare (Optimal)

### Intuition

Use two pointers: `slow` moves one step at a time, `fast` moves two steps. If the list has a cycle, `fast` will eventually lap `slow` and they will meet. If there is no cycle, `fast` will reach null.

**Why they must meet:** Once both pointers are inside the cycle, the gap between them decreases by 1 each step (since fast gains 1 step per iteration). Therefore, they must eventually collide.

### Algorithm

1. Initialize `slow = head` and `fast = head`.
2. While `fast` and `fast.next` are not null:
   - Move `slow` one step: `slow = slow.next`.
   - Move `fast` two steps: `fast = fast.next.next`.
   - If `slow == fast`, return `True` (cycle detected).
3. Return `False` (fast reached the end).

### Implementation

```python
class Solution:
    def hasCycle(self, head: Optional[ListNode]) -> bool:
        slow = head
        fast = head

        while fast and fast.next:
            slow = slow.next
            fast = fast.next.next
            if slow == fast:
                return True

        return False
```

### Complexity Analysis
- **Time Complexity**: O(n) -- in the worst case, the fast pointer traverses the list twice before meeting slow.
- **Space Complexity**: O(1) -- only two pointer variables used.

## Edge Cases

| Edge Case | Input | Expected Output | Why It Matters |
|---|---|---|---|
| Empty list | `head = None` | `False` | No nodes, no cycle |
| Single node, no cycle | `[1], pos = -1` | `False` | Trivially no cycle |
| Single node, self-loop | `[1], pos = 0` | `True` | Node points to itself |
| Two nodes, cycle | `[1,2], pos = 0` | `True` | Tail points back to head |
| Long list, no cycle | `[1,2,...,10000], pos = -1` | `False` | Performance test |
| Cycle at the end | `[1,2,3], pos = 2` | `True` | Last node points to itself |

## Related Problems

| Problem | Difficulty | Key Similarity |
|---|---|---|
| [Linked List Cycle II (LC #142)](https://leetcode.com/problems/linked-list-cycle-ii/) | Medium | Find the start of the cycle using Floyd's |
| [Happy Number (LC #202)](https://leetcode.com/problems/happy-number/) | Easy | Cycle detection in a sequence |
| [Find the Duplicate Number (LC #287)](https://leetcode.com/problems/find-the-duplicate-number/) | Medium | Floyd's algorithm on array indices |
| [Intersection of Two Linked Lists (LC #160)](https://leetcode.com/problems/intersection-of-two-linked-lists/) | Easy | Two-pointer technique on linked lists |

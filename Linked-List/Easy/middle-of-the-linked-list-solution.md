# Middle of the Linked List

## Problem Information
- **Difficulty**: Easy
- **Category**: Linked-List
- **Tags**: Linked List, Two Pointers
- **LeetCode Link**: https://leetcode.com/problems/middle-of-the-linked-list/

## Problem Statement

Given the `head` of a singly linked list, return the middle node of the linked list.

If there are two middle nodes, return **the second middle** node.

### Examples

**Example 1:**
```
Input: head = [1,2,3,4,5]
Output: [3,4,5]
Explanation: The middle node of the list is node 3.
```

**Example 2:**
```
Input: head = [1,2,3,4,5,6]
Output: [4,5,6]
Explanation: Since the list has two middle nodes with values 3 and 4, we return the second one.
```

### Constraints

- The number of nodes in the list is in the range `[1, 100]`.
- `1 <= Node.val <= 100`

## Core Concept / Pattern

**Slow and Fast Pointers (Tortoise and Hare)**: Move `slow` one step at a time and `fast` two steps at a time. When `fast` reaches the end, `slow` is at the middle. This avoids the need to know the list's length in advance.

## Approach 1: Two-Pass (Count then Traverse)

### Intuition

First, count the total number of nodes. Then traverse again to the node at position `count // 2`.

### Algorithm

1. Traverse the entire list to count nodes (`n`).
2. Traverse again, stopping at node `n // 2` (0-indexed).
3. Return that node.

### Implementation

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next

class Solution:
    def middleNode(self, head: Optional[ListNode]) -> Optional[ListNode]:
        # First pass: count nodes
        count = 0
        curr = head
        while curr:
            count += 1
            curr = curr.next

        # Second pass: go to middle
        curr = head
        for _ in range(count // 2):
            curr = curr.next

        return curr
```

### Complexity Analysis
- **Time Complexity**: O(n) -- two passes through the list.
- **Space Complexity**: O(1) -- only counters and pointers.

### Why We Can Do Better

Two passes are not strictly necessary. We can find the middle in a single pass using the fast/slow pointer technique.

## Approach 2: Slow and Fast Pointers (Optimal)

### Intuition

If `fast` moves at double the speed of `slow`, by the time `fast` reaches the end, `slow` has covered exactly half the distance -- placing it at the middle node.

For even-length lists, we want the **second** middle node. The condition `while fast and fast.next` ensures `slow` lands on the second middle when the length is even.

### Algorithm

1. Initialize `slow = head`, `fast = head`.
2. While `fast` and `fast.next` are not null:
   - Move `slow` one step.
   - Move `fast` two steps.
3. Return `slow`.

### Implementation

```python
class Solution:
    def middleNode(self, head: Optional[ListNode]) -> Optional[ListNode]:
        slow = head
        fast = head

        while fast and fast.next:
            slow = slow.next
            fast = fast.next.next

        return slow
```

### Complexity Analysis
- **Time Complexity**: O(n) -- single pass; fast pointer traverses at most n nodes.
- **Space Complexity**: O(1) -- only two pointer variables.

## Approach 3: Array Conversion

### Intuition

Store all nodes in an array, then return the element at index `len // 2`.

### Implementation

```python
class Solution:
    def middleNode(self, head: Optional[ListNode]) -> Optional[ListNode]:
        nodes = []
        curr = head
        while curr:
            nodes.append(curr)
            curr = curr.next

        return nodes[len(nodes) // 2]
```

### Complexity Analysis
- **Time Complexity**: O(n)
- **Space Complexity**: O(n) -- for the array.

## Edge Cases

| Edge Case | Input | Expected Output | Why It Matters |
|---|---|---|---|
| Single node | `[1]` | `[1]` | Trivially the middle |
| Two nodes | `[1,2]` | `[2]` | Return the second middle |
| Three nodes | `[1,2,3]` | `[2,3]` | Odd length, clear middle |
| Even length | `[1,2,3,4,5,6]` | `[4,5,6]` | Must return second middle |
| Odd length | `[1,2,3,4,5]` | `[3,4,5]` | Exact middle |

## Related Problems

| Problem | Difficulty | Key Similarity |
|---|---|---|
| [Linked List Cycle (LC #141)](https://leetcode.com/problems/linked-list-cycle/) | Easy | Same slow/fast pointer technique |
| [Linked List Cycle II (LC #142)](https://leetcode.com/problems/linked-list-cycle-ii/) | Medium | Extended use of slow/fast pointers |
| [Palindrome Linked List (LC #234)](https://leetcode.com/problems/palindrome-linked-list/) | Easy | Find middle, then reverse second half |
| [Sort List (LC #148)](https://leetcode.com/problems/sort-list/) | Medium | Finding middle to split for merge sort |
| [Reorder List (LC #143)](https://leetcode.com/problems/reorder-list/) | Medium | Find middle, reverse, then interleave |
| [Delete the Middle Node (LC #2095)](https://leetcode.com/problems/delete-the-middle-node-of-a-linked-list/) | Medium | Find middle with slow/fast, then delete |

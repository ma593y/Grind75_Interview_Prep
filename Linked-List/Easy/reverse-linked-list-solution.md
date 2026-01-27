# Reverse Linked List

## Problem Information
- **Difficulty**: Easy
- **Category**: Linked-List
- **Tags**: Linked List, Recursion
- **LeetCode Link**: https://leetcode.com/problems/reverse-linked-list/

## Problem Statement

Given the `head` of a singly linked list, reverse the list, and return the reversed list.

### Examples

**Example 1:**
```
Input: head = [1,2,3,4,5]
Output: [5,4,3,2,1]
```

**Example 2:**
```
Input: head = [1,2]
Output: [2,1]
```

**Example 3:**
```
Input: head = []
Output: []
```

### Constraints

- The number of nodes in the list is the range `[0, 5000]`.
- `-5000 <= Node.val <= 5000`

**Follow up:** A linked list can be reversed either iteratively or recursively. Could you implement both?

## Core Concept / Pattern

Reversing a linked list means making each node's `next` pointer point to its previous node instead of its next node. The original head becomes the tail (pointing to null), and the original tail becomes the new head.

## Approach 1: Brute Force (Using Array)

### Intuition

Collect all values into an array, then rebuild the list in reverse order.

### Algorithm

1. Traverse the list, storing all values in a list.
2. Reverse the values list (or iterate it backwards).
3. Create a new linked list from the reversed values.

### Implementation

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next

class Solution:
    def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        vals = []
        curr = head
        while curr:
            vals.append(curr.val)
            curr = curr.next

        dummy = ListNode(0)
        curr = dummy
        for v in reversed(vals):
            curr.next = ListNode(v)
            curr = curr.next

        return dummy.next
```

### Complexity Analysis
- **Time Complexity**: O(n) -- two passes through the list.
- **Space Complexity**: O(n) -- for the values array and new nodes.

### Why We Can Do Better

We can reverse the list in-place by re-pointing the `next` pointers, avoiding the extra array and new node allocations.

## Approach 2: Iterative (Optimal)

### Intuition

Maintain three pointers: `prev` (initially null), `curr` (initially head), and a temporary `next_node`. At each step, point `curr.next` to `prev`, then advance all pointers forward. When `curr` becomes null, `prev` is the new head.

### Algorithm

1. Initialize `prev = None`, `curr = head`.
2. While `curr` is not null:
   a. Save `next_node = curr.next`.
   b. Reverse the link: `curr.next = prev`.
   c. Advance: `prev = curr`, `curr = next_node`.
3. Return `prev` (the new head).

### Implementation

```python
class Solution:
    def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        prev = None
        curr = head

        while curr:
            next_node = curr.next  # Save next
            curr.next = prev       # Reverse link
            prev = curr            # Advance prev
            curr = next_node       # Advance curr

        return prev
```

### Complexity Analysis
- **Time Complexity**: O(n) -- single pass through the list.
- **Space Complexity**: O(1) -- only three pointer variables.

## Approach 3: Recursive

### Intuition

Recursively reverse the rest of the list, then fix the current node's link. The base case is when we reach the last node (or null), which becomes the new head.

### Algorithm

1. **Base case**: If `head` is null or `head.next` is null, return `head`.
2. **Recursive step**: `new_head = reverseList(head.next)`.
3. **Fix pointers**: `head.next.next = head` (the node after head should now point back to head).
4. **Break old link**: `head.next = None`.
5. Return `new_head`.

### Implementation

```python
class Solution:
    def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        # Base case: empty list or single node
        if not head or not head.next:
            return head

        # Recursively reverse the rest
        new_head = self.reverseList(head.next)

        # head.next is the last node of the reversed sublist
        # Make it point back to head
        head.next.next = head
        head.next = None

        return new_head
```

### Complexity Analysis
- **Time Complexity**: O(n) -- n recursive calls.
- **Space Complexity**: O(n) -- recursion stack depth.

## Edge Cases

| Edge Case | Input | Expected Output | Why It Matters |
|---|---|---|---|
| Empty list | `[]` | `[]` | Handle null head |
| Single node | `[1]` | `[1]` | Already reversed |
| Two nodes | `[1,2]` | `[2,1]` | Minimal reversal |
| Already sorted | `[1,2,3,4,5]` | `[5,4,3,2,1]` | Standard case |
| All same values | `[7,7,7]` | `[7,7,7]` | Values unchanged, but pointers reversed |

## Related Problems

| Problem | Difficulty | Key Similarity |
|---|---|---|
| [Reverse Linked List II (LC #92)](https://leetcode.com/problems/reverse-linked-list-ii/) | Medium | Reverse a sub-portion of the list |
| [Palindrome Linked List (LC #234)](https://leetcode.com/problems/palindrome-linked-list/) | Easy | Reverse half and compare |
| [Reverse Nodes in k-Group (LC #25)](https://leetcode.com/problems/reverse-nodes-in-k-group/) | Hard | Reverse segments of size k |
| [Swap Nodes in Pairs (LC #24)](https://leetcode.com/problems/swap-nodes-in-pairs/) | Medium | Reverse in pairs |
| [Reorder List (LC #143)](https://leetcode.com/problems/reorder-list/) | Medium | Uses reversal as a subroutine |

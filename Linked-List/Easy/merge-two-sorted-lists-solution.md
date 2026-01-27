# Merge Two Sorted Lists

## Problem Information
- **Difficulty**: Easy
- **Category**: Linked-List
- **Tags**: Linked List, Recursion
- **LeetCode Link**: https://leetcode.com/problems/merge-two-sorted-lists/

## Problem Statement

You are given the heads of two sorted linked lists `list1` and `list2`. Merge the two lists into one **sorted** list. The list should be made by splicing together the nodes of the first two lists. Return the head of the merged linked list.

### Examples

**Example 1:**
```
Input: list1 = [1,2,4], list2 = [1,3,4]
Output: [1,1,2,3,4,4]
```

**Example 2:**
```
Input: list1 = [], list2 = []
Output: []
```

**Example 3:**
```
Input: list1 = [], list2 = [0]
Output: [0]
```

### Constraints

- The number of nodes in both lists is in the range `[0, 50]`.
- `-100 <= Node.val <= 100`
- Both `list1` and `list2` are sorted in non-decreasing order.

## Core Concept / Pattern

This is the **merge step** of merge sort applied to linked lists. Compare the heads of both lists, pick the smaller one, and advance that pointer. Repeat until one list is exhausted, then attach the remainder.

## Approach 1: Brute Force (Collect, Sort, Rebuild)

### Intuition

Extract all values into an array, sort the array, and build a new linked list from the sorted values.

### Algorithm

1. Traverse both lists, collecting all values into a Python list.
2. Sort the list.
3. Create a new linked list from the sorted values.

### Implementation

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next

class Solution:
    def mergeTwoLists(self, list1: Optional[ListNode], list2: Optional[ListNode]) -> Optional[ListNode]:
        vals = []

        curr = list1
        while curr:
            vals.append(curr.val)
            curr = curr.next

        curr = list2
        while curr:
            vals.append(curr.val)
            curr = curr.next

        vals.sort()

        dummy = ListNode(0)
        curr = dummy
        for v in vals:
            curr.next = ListNode(v)
            curr = curr.next

        return dummy.next
```

### Complexity Analysis
- **Time Complexity**: O((n + m) log(n + m)) -- dominated by the sort.
- **Space Complexity**: O(n + m) -- for the values array and new nodes.

### Why We Can Do Better

Both lists are already sorted. We can merge them in O(n + m) time by exploiting the sorted order, just like the merge step of merge sort.

## Approach 2: Iterative Merge (Optimal)

### Intuition

Use a **dummy head** node and a `tail` pointer. At each step, compare the current heads of both lists, attach the smaller node to `tail`, and advance that list's pointer. When one list is exhausted, attach the remainder of the other list.

### Algorithm

1. Create a dummy node. Set `tail = dummy`.
2. While both `list1` and `list2` are non-null:
   - If `list1.val <= list2.val`, set `tail.next = list1` and advance `list1`.
   - Else, set `tail.next = list2` and advance `list2`.
   - Advance `tail`.
3. Attach the remaining non-null list to `tail.next`.
4. Return `dummy.next`.

### Implementation

```python
class Solution:
    def mergeTwoLists(self, list1: Optional[ListNode], list2: Optional[ListNode]) -> Optional[ListNode]:
        dummy = ListNode(0)
        tail = dummy

        while list1 and list2:
            if list1.val <= list2.val:
                tail.next = list1
                list1 = list1.next
            else:
                tail.next = list2
                list2 = list2.next
            tail = tail.next

        # Attach the remaining nodes
        tail.next = list1 if list1 else list2

        return dummy.next
```

### Complexity Analysis
- **Time Complexity**: O(n + m) -- each node is visited exactly once.
- **Space Complexity**: O(1) -- only a constant number of pointers are used (no new nodes created).

## Approach 3: Recursive

### Intuition

Recursively define: the merged list starts with the smaller head, and its `next` is the merge of the remaining elements.

### Implementation

```python
class Solution:
    def mergeTwoLists(self, list1: Optional[ListNode], list2: Optional[ListNode]) -> Optional[ListNode]:
        if not list1:
            return list2
        if not list2:
            return list1

        if list1.val <= list2.val:
            list1.next = self.mergeTwoLists(list1.next, list2)
            return list1
        else:
            list2.next = self.mergeTwoLists(list1, list2.next)
            return list2
```

### Complexity Analysis
- **Time Complexity**: O(n + m)
- **Space Complexity**: O(n + m) -- recursion stack depth.

## Edge Cases

| Edge Case | Input | Expected Output | Why It Matters |
|---|---|---|---|
| Both empty | `[], []` | `[]` | Handle null inputs |
| One empty | `[], [0]` | `[0]` | Directly return the non-empty list |
| Different lengths | `[1,2], [1,3,4,5]` | `[1,1,2,3,4,5]` | One list exhausted first |
| Duplicates | `[1,1], [1,1]` | `[1,1,1,1]` | Equals go to either side |
| Single elements | `[2], [1]` | `[1,2]` | Minimal merge |

## Related Problems

| Problem | Difficulty | Key Similarity |
|---|---|---|
| [Merge k Sorted Lists (LC #23)](https://leetcode.com/problems/merge-k-sorted-lists/) | Hard | Generalization to k lists using heap |
| [Sort List (LC #148)](https://leetcode.com/problems/sort-list/) | Medium | Merge sort on linked list uses this merge step |
| [Merge Sorted Array (LC #88)](https://leetcode.com/problems/merge-sorted-array/) | Easy | Same merge concept with arrays |
| [Merge In Between Linked Lists (LC #1669)](https://leetcode.com/problems/merge-in-between-linked-lists/) | Medium | Linked list splicing |

# Merge Two Sorted Lists - Study Guide

## Problem Summary

> **One-liner**: Merge two sorted linked lists into a single sorted linked list by re-linking existing nodes.

## Key Intuition

Since both lists are already sorted, we can merge them in a single pass -- exactly like the **merge step of merge sort**. Compare the two heads, pick the smaller, advance that pointer, and repeat. A **dummy head node** eliminates the need for special-case logic to initialize the merged list.

## Approaches Overview

| Approach | Time | Space | Pros | Cons |
|---|---|---|---|---|
| Iterative with Dummy Node | O(n+m) | O(1) | Optimal, clean, no extra memory | Slightly more code than recursive |
| Recursive | O(n+m) | O(n+m) | Elegant, concise | Stack overflow risk for long lists |

## Approach 1: Iterative with Dummy Node

### Core Idea

Create a dummy node that serves as the starting point of the merged list. Maintain a `tail` pointer that always points to the last node of the merged list. At each step, compare the heads of both lists, attach the smaller one to `tail.next`, and advance the corresponding pointer.

### Code

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

        tail.next = list1 if list1 else list2
        return dummy.next
```

### Walkthrough

Example: `list1 = [1,2,4]`, `list2 = [1,3,4]`

| Step | list1 | list2 | Compare | Attach | Merged so far |
|------|-------|-------|---------|--------|---------------|
| 1 | 1->2->4 | 1->3->4 | 1 <= 1 | list1 node (1) | [1] |
| 2 | 2->4 | 1->3->4 | 2 > 1 | list2 node (1) | [1,1] |
| 3 | 2->4 | 3->4 | 2 <= 3 | list1 node (2) | [1,1,2] |
| 4 | 4 | 3->4 | 4 > 3 | list2 node (3) | [1,1,2,3] |
| 5 | 4 | 4 | 4 <= 4 | list1 node (4) | [1,1,2,3,4] |
| 6 | null | 4 | loop ends | attach list2 remainder | [1,1,2,3,4,4] |

## Approach 2: Recursive (Optimal in elegance)

### Core Idea

Base case: if either list is empty, return the other. Recursive case: the merged list starts with whichever head is smaller; that node's `next` is the result of recursively merging the rest.

### Code

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

### Walkthrough

Example: `list1 = [1,2,4]`, `list2 = [1,3,4]`

```
merge([1,2,4], [1,3,4])
  1 <= 1: list1.next = merge([2,4], [1,3,4])
    2 > 1: list2.next = merge([2,4], [3,4])
      2 <= 3: list1.next = merge([4], [3,4])
        4 > 3: list2.next = merge([4], [4])
          4 <= 4: list1.next = merge([], [4])
            list1 is null -> return [4]
          return [4, 4]
        return [3, 4, 4]
      return [2, 3, 4, 4]
    return [1, 2, 3, 4, 4]
  return [1, 1, 2, 3, 4, 4]
```

## Complexity Analysis

| Approach | Time | Space | Notes |
|---|---|---|---|
| Iterative | O(n + m) | O(1) | In-place re-linking, only pointer variables |
| Recursive | O(n + m) | O(n + m) | Call stack depth equals total number of nodes |

## Common Mistakes

1. **Forgetting the dummy node.** Without it, you need awkward conditional logic to initialize the head of the merged list.
2. **Not attaching the remaining list.** After the while loop, one list may still have nodes. Must set `tail.next = list1 if list1 else list2`.
3. **Advancing `tail` at the wrong time.** Always advance `tail` after attaching a node, not before.
4. **Modifying node values instead of re-linking.** The problem asks to splice nodes, not create new ones.
5. **Using `<` instead of `<=`.** Both work for correctness, but `<=` preserves the relative order of equal elements (stability).

## Interview Tips

- **Mention the dummy node pattern immediately.** It is a fundamental linked list technique that simplifies code dramatically.
- **Default to iterative.** Unless the interviewer asks for recursion, the iterative version is preferred because it uses O(1) space.
- **Connect to merge sort.** Saying "this is the merge step of merge sort" shows strong algorithmic knowledge.
- **Discuss in-place merging.** Point out that no new nodes are created (in the iterative approach) -- existing nodes are simply re-linked.
- **Know the generalization.** If asked about merging k sorted lists, mention using a min-heap to generalize this two-list merge.

## Mnemonics / Memory Aids

- **"Dummy, Compare, Attach, Advance"** -- the four operations in each iteration.
- **"Tail chases the smaller head"** -- `tail.next` always points to the node with the smaller value.
- **"Leftovers get linked"** -- after the loop, just link the remaining non-null list.
- **Think "zipper"** -- you are zipping two sorted sequences together.

## Self-Assessment Checklist

- [ ] Can I write the iterative solution from scratch in under 5 minutes?
- [ ] Can I explain why a dummy node is used and what happens without it?
- [ ] Can I trace through [1,2,4] and [1,3,4] step by step?
- [ ] Can I write the recursive solution and explain the base cases?
- [ ] Do I understand why the iterative approach is O(1) space while recursive is O(n+m)?
- [ ] Can I explain how this extends to merging k sorted lists?

# Reverse Linked List - Study Guide

## Problem Summary

> **One-liner**: Reverse a singly linked list in-place so the original tail becomes the new head and all `next` pointers are flipped.

## Key Intuition

Reversing a linked list means flipping the direction of every `next` pointer. The core operation is: save the next node, point the current node backwards, then move forward. You need three pointers to avoid losing references: `prev`, `curr`, and `next_node`.

## Approaches Overview

| Approach | Time | Space | Pros | Cons |
|---|---|---|---|---|
| Iterative (three pointers) | O(n) | O(1) | Optimal, easy to understand | Need to track three pointers |
| Recursive | O(n) | O(n) | Elegant, short code | Uses call stack, harder to debug |

## Approach 1: Iterative (Three Pointers)

### Core Idea

Walk through the list with `prev` and `curr`. At each node, save `curr.next`, reverse the pointer (`curr.next = prev`), then advance both `prev` and `curr`. When `curr` is null, `prev` is the new head.

### Code

```python
class Solution:
    def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        prev = None
        curr = head

        while curr:
            next_node = curr.next  # Save next
            curr.next = prev       # Reverse
            prev = curr            # Advance prev
            curr = next_node       # Advance curr

        return prev
```

### Walkthrough

Example: `[1, 2, 3, 4, 5]`

```
Initial:  prev=None, curr=1->2->3->4->5

Step 1:   next_node = 2
          1.next = None (was 2)
          prev=1, curr=2
          State: None<-1  2->3->4->5

Step 2:   next_node = 3
          2.next = 1 (was 3)
          prev=2, curr=3
          State: None<-1<-2  3->4->5

Step 3:   next_node = 4
          3.next = 2 (was 4)
          prev=3, curr=4
          State: None<-1<-2<-3  4->5

Step 4:   next_node = 5
          4.next = 3 (was 5)
          prev=4, curr=5
          State: None<-1<-2<-3<-4  5

Step 5:   next_node = None
          5.next = 4 (was None)
          prev=5, curr=None
          State: None<-1<-2<-3<-4<-5

curr is None -> return prev = 5
Result: [5, 4, 3, 2, 1]
```

## Approach 2: Recursive (Optimal in elegance)

### Core Idea

Recurse to the end of the list. The last node is the new head. As recursion unwinds, make each node's next node point back to it, then disconnect the forward pointer.

The key line is `head.next.next = head` -- the node that was after `head` now points back to `head`.

### Code

```python
class Solution:
    def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        if not head or not head.next:
            return head

        new_head = self.reverseList(head.next)
        head.next.next = head
        head.next = None

        return new_head
```

### Walkthrough

Example: `[1, 2, 3]`

```
reverseList(1->2->3)
  reverseList(2->3)
    reverseList(3)
      Base case: return 3 (new_head)
    head=2: head.next.next = head  =>  3.next = 2
    head=2: head.next = None       =>  2.next = None
    State: 3->2->None, return new_head=3
  head=1: head.next.next = head  =>  2.next = 1
  head=1: head.next = None       =>  1.next = None
  State: 3->2->1->None, return new_head=3

Result: [3, 2, 1]
```

## Complexity Analysis

| Approach | Time | Space | Notes |
|---|---|---|---|
| Iterative | O(n) | O(1) | True in-place reversal |
| Recursive | O(n) | O(n) | Call stack depth = n |

## Common Mistakes

1. **Forgetting to save `curr.next` before overwriting.** If you write `curr.next = prev` first, you lose the reference to the rest of the list.
2. **Returning `curr` instead of `prev`.** When the loop exits, `curr` is `None`. The new head is `prev`.
3. **Not setting the original head's next to None.** In the recursive approach, the original head must end up with `next = None` or you get a cycle.
4. **Off-by-one in the recursive base case.** The base case should handle both `head is None` (empty list) and `head.next is None` (single node).
5. **Confusing `head.next.next = head` with `head.next = head`.** The first correctly makes the node after `head` point back; the second creates a self-loop.

## Interview Tips

- **This is the most fundamental linked list operation.** You must be able to write it flawlessly from memory.
- **Draw the pointers.** Sketching boxes and arrows on a whiteboard makes the logic clear to both you and the interviewer.
- **Know both iterative and recursive.** Most interviewers will ask for one and then ask if you can do the other.
- **Explain the invariant.** At any point in the iterative solution: all nodes before `curr` are reversed, all nodes from `curr` onward are still in original order.
- **This is a building block.** Many medium/hard problems use list reversal as a subroutine (Palindrome Linked List, Reverse Nodes in k-Group, Reorder List).

## Mnemonics / Memory Aids

- **"Save, Reverse, Advance, Advance"** -- the four steps inside the loop:
  1. Save `next_node = curr.next`
  2. Reverse `curr.next = prev`
  3. Advance `prev = curr`
  4. Advance `curr = next_node`
- **"prev starts null, curr starts head, return prev"** -- the initialization and termination.
- **Recursive mantra: "head.next.next = head; head.next = None"** -- the two lines that reverse the link during unwinding.
- **"Three pointers, four lines"** -- the iterative solution has exactly four lines inside the while loop.

## Self-Assessment Checklist

- [ ] Can I write the iterative solution from scratch in under 3 minutes?
- [ ] Can I draw the pointer state after each step for a 4-node list?
- [ ] Can I write the recursive solution and explain the `head.next.next = head` line?
- [ ] Do I understand why `prev` is the new head (not `curr`)?
- [ ] Can I explain the space complexity difference between iterative and recursive?
- [ ] Can I apply reversal as a subroutine (e.g., reverse a sub-portion of a list)?

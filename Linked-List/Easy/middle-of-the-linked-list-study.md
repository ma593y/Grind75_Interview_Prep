# Middle of the Linked List - Study Guide

## Problem Summary

> **One-liner**: Return the middle node of a singly linked list (if two middles exist, return the second one) using the slow-and-fast pointer technique in a single pass.

## Key Intuition

When one pointer moves **twice as fast** as another, the slow pointer will be at the **halfway point** when the fast pointer reaches the end. This is the classic **tortoise and hare** pattern applied to finding the midpoint rather than detecting cycles.

## Approaches Overview

| Approach | Time | Space | Key Idea |
|---|---|---|---|
| Two-Pass (Count then Index) | O(n) | O(1) | Count nodes first, then traverse to n/2 |
| Array Storage | O(n) | O(n) | Store all nodes, index into middle |
| Slow & Fast Pointers | O(n) | O(1) | Single pass, fast moves 2x speed |

## Approach 1: Two-Pass

### Core Idea

Count the total number of nodes in one pass, then traverse `count // 2` nodes in a second pass to reach the middle.

### Code

```python
class Solution:
    def middleNode(self, head: Optional[ListNode]) -> Optional[ListNode]:
        count = 0
        curr = head
        while curr:
            count += 1
            curr = curr.next

        curr = head
        for _ in range(count // 2):
            curr = curr.next

        return curr
```

### Walkthrough

Example: `[1, 2, 3, 4, 5]`

- Pass 1: count = 5
- Middle index = 5 // 2 = 2
- Pass 2: advance 2 steps from head -> node 3
- Return node 3

Example: `[1, 2, 3, 4, 5, 6]`

- Pass 1: count = 6
- Middle index = 6 // 2 = 3
- Pass 2: advance 3 steps from head -> node 4
- Return node 4 (second middle)

## Approach 2: Slow & Fast Pointers (Optimal)

### Core Idea

Initialize both `slow` and `fast` at `head`. Move `slow` one step and `fast` two steps in each iteration. When `fast` cannot move further (reaches null or has no next), `slow` is at the middle.

### Code

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

### Walkthrough

**Odd length**: `[1, 2, 3, 4, 5]`

| Step | slow | fast | fast.next |
|------|------|------|-----------|
| Start | 1 | 1 | 2 |
| 1 | 2 | 3 | 4 |
| 2 | 3 | 5 | None |

`fast.next` is None, loop ends. Return `slow` = node 3.

**Even length**: `[1, 2, 3, 4, 5, 6]`

| Step | slow | fast | fast.next |
|------|------|------|-----------|
| Start | 1 | 1 | 2 |
| 1 | 2 | 3 | 4 |
| 2 | 3 | 5 | 6 |
| 3 | 4 | None | -- |

`fast` is None, loop ends. Return `slow` = node 4 (second middle).

**Why it returns the second middle for even lengths**: After the last iteration, `fast` has gone past the end (to None via `fast.next.next`), and `slow` has taken one extra step to land on the second middle.

## Complexity Analysis

| Approach | Time | Space | Notes |
|---|---|---|---|
| Two-Pass | O(n) | O(1) | Two traversals, but O(1) space |
| Array | O(n) | O(n) | Simple but uses extra memory |
| Slow & Fast | O(n) | O(1) | Single pass, optimal |

## Common Mistakes

1. **Getting the wrong middle for even-length lists.** The problem specifies returning the **second** middle. The condition `while fast and fast.next` handles this correctly. Using `while fast.next and fast.next.next` would return the **first** middle.
2. **Null pointer errors with `fast.next.next`.** Always check `fast and fast.next` before accessing `fast.next.next`.
3. **Off-by-one in the two-pass approach.** `count // 2` gives the correct 0-based index for the second middle.
4. **Not handling single-node lists.** A single node is its own middle. The loop condition `fast and fast.next` correctly does not execute when `head.next` is None.

## Interview Tips

- **This is a foundational technique.** The slow/fast pointer pattern is a building block for many linked list problems (palindrome check, sort list, reorder list, cycle detection).
- **Explain the speed ratio.** "When fast has traveled the full length, slow has traveled half" -- this one sentence captures the entire algorithm.
- **Know the variant for first middle.** If you need the first middle for even-length lists, use `while fast.next and fast.next.next` instead. This matters in problems like Sort List where you split before the middle.
- **Practice drawing it.** On a whiteboard, draw 5 nodes and 6 nodes, then trace both pointers step by step. This cements the pattern.
- **Mention use cases.** Finding the middle is step 1 in merge sort on linked lists, checking if a linked list is a palindrome, and reordering a linked list.

## Mnemonics / Memory Aids

- **"Fast finishes, slow is in the middle"** -- the one-sentence summary.
- **"1x and 2x"** -- slow moves 1 step, fast moves 2 steps.
- **"fast AND fast.next"** -- the exact while loop guard (same as cycle detection).
- **"Second middle = `while fast and fast.next`"** -- the default version returns the second middle for even-length lists.
- **"First middle = `while fast.next and fast.next.next`"** -- use this when you need the first middle.

## Self-Assessment Checklist

- [ ] Can I write the slow/fast pointer solution from scratch in under 3 minutes?
- [ ] Can I trace through both odd and even length lists and show where `slow` ends up?
- [ ] Do I know how to modify the loop condition to get the first middle instead of the second?
- [ ] Can I explain why `while fast and fast.next` gives the second middle for even-length lists?
- [ ] Do I know how finding the middle is used as a subroutine in Sort List and Palindrome Linked List?
- [ ] Can I compare this approach to the two-pass counting approach and explain why single pass is preferred?

# Linked List Cycle - Study Guide

## Problem Summary

> **One-liner**: Determine whether a singly linked list contains a cycle by detecting if any node is visited more than once during traversal.

## Key Intuition

If you walk through a linked list and it has a cycle, you will loop forever. **Floyd's Tortoise and Hare algorithm** exploits the fact that a fast pointer (2 steps) and a slow pointer (1 step) moving simultaneously will always meet inside a cycle. Think of two runners on a circular track -- the faster one always laps the slower one.

## Approaches Overview

| Approach | Time | Space | Key Idea |
|---|---|---|---|
| Hash Set | O(n) | O(n) | Track visited nodes, detect revisit |
| Floyd's Tortoise and Hare | O(n) | O(1) | Fast/slow pointers meet in a cycle |

## Approach 1: Hash Set

### Core Idea

Store each visited node's reference in a set. If we encounter a node already in the set, there is a cycle. If we reach `None`, there is no cycle.

### Code

```python
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

### Walkthrough

Example: `[3, 2, 0, -4]` with tail connecting to node at index 1 (value 2).

| Step | Node | In visited? | Action |
|------|------|-------------|--------|
| 1 | 3 | No | Add to set |
| 2 | 2 | No | Add to set |
| 3 | 0 | No | Add to set |
| 4 | -4 | No | Add to set |
| 5 | 2 | Yes | Return True |

## Approach 2: Floyd's Tortoise and Hare (Optimal)

### Core Idea

Two pointers start at head. Slow moves 1 step, fast moves 2 steps. If they ever point to the same node, a cycle exists. If fast reaches null, no cycle exists.

### Code

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

### Walkthrough

Example: `[3, 2, 0, -4]` with tail connecting to index 1 (node with value 2).

Nodes by position: `n0(3) -> n1(2) -> n2(0) -> n3(-4) -> n1(2) -> ...`

| Step | slow | fast | Meet? |
|------|------|------|-------|
| Start | n0(3) | n0(3) | -- |
| 1 | n1(2) | n2(0) | No |
| 2 | n2(0) | n1(2) | No |
| 3 | n3(-4) | n3(-4) | Yes! Return True |

**Why they meet**: Once both are in the cycle, the distance between them decreases by 1 each step (fast gains 1 step on slow). So they must collide.

**Mathematical proof**: Let the cycle have length `C`. Once slow enters the cycle, fast is already inside. The gap between them (mod C) decreases by 1 each iteration: `C, C-1, C-2, ..., 1, 0`. At gap 0, they meet.

## Complexity Analysis

| Approach | Time | Space | Notes |
|---|---|---|---|
| Hash Set | O(n) | O(n) | Simple but uses extra memory |
| Floyd's Algorithm | O(n) | O(1) | Optimal; only two pointers |

## Common Mistakes

1. **Checking `slow == fast` before moving.** Both start at `head`, so they trivially match at the beginning. Move first, then compare.
2. **Only checking `fast` for null.** You must check both `fast and fast.next` to avoid a null pointer error when accessing `fast.next.next`.
3. **Comparing node values instead of node references.** Two different nodes can have the same value. Compare the actual node objects (`is` or `==` on references), not `.val`.
4. **Forgetting the empty list case.** If `head` is `None`, the while loop never executes, and we correctly return `False`.

## Interview Tips

- **Name the algorithm.** Saying "Floyd's Cycle Detection" or "Tortoise and Hare" immediately signals expertise.
- **Explain why they must meet.** The gap-reduction argument is simple and convincing: the gap decreases by 1 each step.
- **Know the follow-up: finding the cycle start.** In LC #142, once slow and fast meet, reset one pointer to head and move both at speed 1. They meet at the cycle's starting node.
- **Mention other applications.** Floyd's algorithm also works for detecting cycles in sequences (Happy Number) and finding duplicates in arrays (LC #287).
- **Start with the hash set approach.** It is simpler and correct. Then optimize to Floyd's. This shows structured problem-solving.

## Mnemonics / Memory Aids

- **"Tortoise and Hare"** -- slow (tortoise) moves 1 step, fast (hare) moves 2 steps.
- **"If fast hits null, no cycle"** -- the termination condition.
- **"Move first, check after"** -- always advance pointers before comparing them.
- **"Gap shrinks by one"** -- inside the cycle, the distance between fast and slow decreases by 1 each iteration.
- **"fast AND fast.next"** -- the while loop guard; memorize this exact condition.

## Self-Assessment Checklist

- [ ] Can I write Floyd's algorithm from scratch in under 5 minutes?
- [ ] Can I explain WHY the two pointers must meet inside a cycle?
- [ ] Do I know the correct while loop condition (`fast and fast.next`)?
- [ ] Can I explain why we check after moving, not before?
- [ ] Can I trace through a list with a cycle and a list without?
- [ ] Do I know how to find the cycle's starting node (LC #142 follow-up)?
- [ ] Can I name other problems that use Floyd's algorithm?

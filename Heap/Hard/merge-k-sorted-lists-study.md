# Merge k Sorted Lists - Study Guide

## Problem Summary
- **Difficulty**: Hard
- **Category**: Heap
- **Tags**: Linked List, Divide and Conquer, Heap, Merge Sort
- **LeetCode Link**: https://leetcode.com/problems/merge-k-sorted-lists/

> **One-liner**: Merge k sorted linked lists into one sorted list using a min-heap to always pick the smallest element.

## Key Intuition
Use a min-heap to efficiently find the smallest element among k list heads. Initially add all list heads to the heap, then repeatedly extract the minimum, add it to the result, and push the next node from that list (if any). The heap ensures O(log k) operations for finding the minimum.

## Approaches Overview

| Approach | Time | Space | Trade-off |
|----------|------|-------|-----------|
| Brute Force (collect & sort) | O(N log N) | O(N) | Simple but inefficient |
| Compare One by One | O(kN) | O(1) | No extra space but slow |
| Min-Heap | O(N log k) | O(k) | Optimal time complexity |
| Divide and Conquer | O(N log k) | O(log k) | Less space, more complex |

## Approach 1: Min-Heap

### Core Idea
Maintain a heap of size k with current heads. Always pop minimum, append to result, push next node.

### Code
```python
import heapq

class Solution:
    def mergeKLists(self, lists):
        heap = []
        dummy = ListNode(0)
        current = dummy

        # Add first node of each list to heap
        for i, lst in enumerate(lists):
            if lst:
                heapq.heappush(heap, (lst.val, i, lst))

        while heap:
            val, i, node = heapq.heappop(heap)
            current.next = node
            current = current.next
            if node.next:
                heapq.heappush(heap, (node.next.val, i, node.next))

        return dummy.next
```

### Walkthrough
Lists: [1→4→5], [1→3→4], [2→6]
1. Heap: [(1,0,node), (1,1,node), (2,2,node)]
2. Pop (1,0), push 4 → result: 1, heap: [(1,1), (2,2), (4,0)]
3. Pop (1,1), push 3 → result: 1→1, heap: [(2,2), (3,1), (4,0)]
4. Continue until heap empty...
Result: 1→1→2→3→4→4→5→6

## Approach 2: Divide and Conquer (Optimal)

### Core Idea
Merge lists pairwise, reducing k lists to k/2, then k/4, etc. Like merge sort.

### Code
```python
class Solution:
    def mergeKLists(self, lists):
        if not lists:
            return None

        def mergeTwoLists(l1, l2):
            dummy = ListNode(0)
            curr = dummy
            while l1 and l2:
                if l1.val <= l2.val:
                    curr.next = l1
                    l1 = l1.next
                else:
                    curr.next = l2
                    l2 = l2.next
                curr = curr.next
            curr.next = l1 or l2
            return dummy.next

        while len(lists) > 1:
            merged = []
            for i in range(0, len(lists), 2):
                l1 = lists[i]
                l2 = lists[i + 1] if i + 1 < len(lists) else None
                merged.append(mergeTwoLists(l1, l2))
            lists = merged

        return lists[0]
```

### Walkthrough
Lists: [L1, L2, L3, L4]
- Round 1: merge(L1,L2), merge(L3,L4) → [M1, M2]
- Round 2: merge(M1,M2) → [Result]
Total: log(k) rounds, each processing N elements

## Complexity Analysis
- **Time**: O(N log k) -- N total nodes, each compared log k times
- **Space**: O(k) for heap approach, O(log k) for divide and conquer (recursion stack)

## Common Mistakes
1. **Python heap with objects**: Need to use tuple (val, index, node) to avoid comparing ListNode objects
2. **Empty lists**: Must handle None lists in input
3. **Off-by-one in divide and conquer**: Handle odd number of lists

## Interview Tips
- **How to communicate**: "I'll use a min-heap of size k to efficiently find the smallest element among all list heads in O(log k) time."
- **Clarifying questions to ask**: "Can lists be empty? Can the input array be empty?"
- **Optimization path**: Brute force O(N log N) → O(kN) sequential → O(N log k) heap/divide-conquer
- **Follow-up variations**: "Merge k sorted arrays" (same approach), "External sort" (similar concept)

## Mnemonics / Memory Aids
**"Heap of Heads"**: Always keep current heads in heap, pop min, push next.

**Divide and Conquer mantra**: "Pair up, merge, repeat until one remains"

## Self-Assessment Checklist
- [ ] Can I identify this problem type within 2 minutes?
- [ ] Can I code the optimal solution without looking at notes?
- [ ] Can I explain the time/space complexity clearly?
- [ ] Can I handle all edge cases?
- [ ] Can I discuss trade-offs between approaches?

# Merge K Sorted Lists

## Problem Information
- **Difficulty**: Hard
- **Category**: Heap / Priority Queue
- **Tags**: Linked List, Heap, Divide and Conquer, Merge Sort
- **LeetCode Link**: https://leetcode.com/problems/merge-k-sorted-lists/

## Problem Statement

You are given an array of `k` linked-lists `lists`, each linked-list is sorted in ascending order.

Merge all the linked-lists into one sorted linked-list and return it.

### Examples

**Example 1:**
```
Input: lists = [[1,4,5],[1,3,4],[2,6]]
Output: [1,1,2,3,4,4,5,6]
Explanation: The linked-lists are:
[1->4->5, 1->3->4, 2->6]
Merging them into one sorted list: 1->1->2->3->4->4->5->6
```

**Example 2:**
```
Input: lists = []
Output: []
```

**Example 3:**
```
Input: lists = [[]]
Output: []
```

### Constraints
- `k == lists.length`
- `0 <= k <= 10^4`
- `0 <= lists[i].length <= 500`
- `-10^4 <= lists[i][j] <= 10^4`
- `lists[i]` is sorted in **ascending order**.
- The sum of `lists[i].length` will not exceed `10^4`.

## Core Concept / Pattern

We need to merge k sorted sequences into one. The key insight is that at each step, we only need to compare the front elements of each list. A **min-heap** efficiently gives us the smallest front element in O(log k) time, leading to an overall O(N log k) solution.

## Approach 1: Brute Force (Merge One by One)

### Intuition
Merge the first two lists, then merge the result with the third list, and so on. This is like repeatedly applying the "merge two sorted lists" operation.

### Algorithm
1. Start with an empty merged list.
2. For each list in the array, merge it with the current result.
3. Return the final merged list.

### Implementation

```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next

class Solution:
    def mergeKLists(self, lists: list[ListNode | None]) -> ListNode | None:
        if not lists:
            return None

        def merge_two(l1: ListNode | None, l2: ListNode | None) -> ListNode | None:
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

        result = lists[0]
        for i in range(1, len(lists)):
            result = merge_two(result, i < len(lists) and lists[i])

        return result
```

### Complexity Analysis
- **Time Complexity**: O(N * k) where N is the total number of nodes. Each merge pass processes up to the entire accumulated length.
- **Space Complexity**: O(1) extra space (in-place linking).

### Why We Can Do Better
Merging one-by-one causes the first lists to be traversed many times. Using a heap or divide-and-conquer, we can reduce repeated work.

## Approach 2: Optimal Solution (Min-Heap)

### Intuition
Maintain a min-heap of size k containing the front node of each list. Repeatedly extract the minimum, append it to the result, and push the next node from that list (if it exists). The heap ensures we always get the global minimum in O(log k).

### Algorithm
1. Add the head of each non-empty list to the min-heap.
2. Create a dummy node for the result list.
3. While the heap is not empty:
   - Pop the smallest node.
   - Append it to the result.
   - If the popped node has a next node, push it to the heap.
4. Return `dummy.next`.

### Implementation

```python
import heapq

class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next

class Solution:
    def mergeKLists(self, lists: list[ListNode | None]) -> ListNode | None:
        # Use index as tiebreaker to avoid comparing ListNode objects
        heap = []
        for i, node in enumerate(lists):
            if node:
                heapq.heappush(heap, (node.val, i, node))

        dummy = ListNode(0)
        curr = dummy

        while heap:
            val, idx, node = heapq.heappop(heap)
            curr.next = node
            curr = curr.next

            if node.next:
                heapq.heappush(heap, (node.next.val, idx, node.next))

        return dummy.next
```

**Alternative (Divide and Conquer):**

```python
class Solution:
    def mergeKLists(self, lists: list[ListNode | None]) -> ListNode | None:
        if not lists:
            return None

        def merge_two(l1, l2):
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
                merged.append(merge_two(l1, l2))
            lists = merged

        return lists[0]
```

### Complexity Analysis

**Min-Heap:**
- **Time Complexity**: O(N log k) -- each of the N nodes is pushed and popped once, each operation O(log k).
- **Space Complexity**: O(k) for the heap.

**Divide and Conquer:**
- **Time Complexity**: O(N log k) -- log k merge rounds, each processing all N nodes.
- **Space Complexity**: O(log k) for the recursion stack (or O(1) iterative).

## Edge Cases

| Edge Case | Expected Behavior | Why It Matters |
|-----------|-------------------|----------------|
| Empty lists array `[]` | Return None | No lists to merge |
| Array with one empty list `[[]]` | Return None | Single empty list |
| Single non-empty list | Return that list | No merging needed |
| Lists of different lengths | All nodes included | Heap handles naturally |
| All lists have same values | Merged list with duplicates | Tiebreaker in heap matters |
| k = 10000, most empty | Handle efficiently | Skip empty lists |

## Related Problems

| Problem | Relationship | Difficulty |
|---------|-------------|------------|
| Merge Two Sorted Lists (LC #21) | Building block for this problem | Easy |
| Ugly Number II (LC #264) | Multiple sorted streams merged | Medium |
| Smallest Range Covering Elements (LC #632) | Sliding window on k sorted lists | Hard |
| Kth Smallest Element in Sorted Matrix (LC #378) | Heap on multiple sorted sequences | Medium |

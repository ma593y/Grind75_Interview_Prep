# Find Median from Data Stream

## Problem Information
- **Difficulty**: Hard
- **Category**: Heap / Priority Queue
- **Tags**: Two Heaps, Sorting, Design, Data Stream
- **LeetCode Link**: https://leetcode.com/problems/find-median-from-data-stream/

## Problem Statement

The **median** is the middle value in an ordered integer list. If the size of the list is even, there is no middle value, and the median is the mean of the two middle values.

Implement the `MedianFinder` class:
- `MedianFinder()` initializes the MedianFinder object.
- `void addNum(int num)` adds the integer `num` from the data stream to the data structure.
- `double findMedian()` returns the median of all elements so far. Answers within `10^-5` of the actual answer will be accepted.

### Examples

**Example 1:**
```
Input:
["MedianFinder", "addNum", "addNum", "findMedian", "addNum", "findMedian"]
[[], [1], [2], [], [3], []]
Output: [null, null, null, 1.5, null, 2.0]

Explanation:
MedianFinder medianFinder = new MedianFinder();
medianFinder.addNum(1);    // arr = [1]
medianFinder.addNum(2);    // arr = [1, 2]
medianFinder.findMedian(); // return 1.5 (i.e., (1 + 2) / 2)
medianFinder.addNum(3);    // arr = [1, 2, 3]
medianFinder.findMedian(); // return 2.0
```

### Constraints
- `-10^5 <= num <= 10^5`
- There will be at least one element in the data structure before calling `findMedian`.
- At most `5 * 10^4` calls will be made to `addNum` and `findMedian`.

## Core Concept / Pattern

The **Two Heaps** pattern splits the data into two halves: a max-heap for the lower half and a min-heap for the upper half. The median is either the top of the max-heap (odd total) or the average of both tops (even total). This allows O(log N) insertion and O(1) median retrieval.

## Approach 1: Brute Force (Sorted List)

### Intuition
Maintain a sorted list. On each insertion, find the correct position and insert. The median is the middle element(s).

### Algorithm
1. Use `bisect.insort` to insert into a sorted list.
2. For findMedian, return the middle element (odd) or average of two middle elements (even).

### Implementation

```python
import bisect

class MedianFinder:
    def __init__(self):
        self.data = []

    def addNum(self, num: int) -> None:
        bisect.insort(self.data, num)

    def findMedian(self) -> float:
        n = len(self.data)
        mid = n // 2
        if n % 2 == 1:
            return float(self.data[mid])
        else:
            return (self.data[mid - 1] + self.data[mid]) / 2.0
```

### Complexity Analysis
- **addNum**: O(N) -- binary search is O(log N) but insertion into a list is O(N) due to shifting.
- **findMedian**: O(1) -- direct index access.
- **Space**: O(N) for the sorted list.

### Why We Can Do Better
The O(N) insertion is the bottleneck. Using two heaps, we can achieve O(log N) insertion while keeping O(1) median retrieval.

## Approach 2: Optimal Solution (Two Heaps)

### Intuition
Split the numbers into two halves:
- **Max-heap (`lo`)**: stores the smaller half (negated values for Python's min-heap).
- **Min-heap (`hi`)**: stores the larger half.

Invariants:
1. Every element in `lo` is less than or equal to every element in `hi`.
2. The heaps are balanced: `len(lo) == len(hi)` or `len(lo) == len(hi) + 1`.

The median is either the top of `lo` (odd count) or the average of both tops (even count).

### Algorithm
1. **addNum(num)**:
   - Push `num` onto `lo` (max-heap, using negation).
   - Move the top of `lo` to `hi` (ensures ordering invariant).
   - If `hi` is larger than `lo`, move the top of `hi` back to `lo` (ensures size invariant).
2. **findMedian()**:
   - If `lo` is larger, return `-lo[0]`.
   - Otherwise, return `(-lo[0] + hi[0]) / 2.0`.

### Implementation

```python
import heapq

class MedianFinder:
    def __init__(self):
        self.lo = []  # max-heap (negated values) for lower half
        self.hi = []  # min-heap for upper half

    def addNum(self, num: int) -> None:
        # Always push to max-heap first
        heapq.heappush(self.lo, -num)

        # Ensure ordering: max of lo <= min of hi
        heapq.heappush(self.hi, -heapq.heappop(self.lo))

        # Ensure size: lo can have at most 1 more element than hi
        if len(self.hi) > len(self.lo):
            heapq.heappush(self.lo, -heapq.heappop(self.hi))

    def findMedian(self) -> float:
        if len(self.lo) > len(self.hi):
            return -self.lo[0]
        return (-self.lo[0] + self.hi[0]) / 2.0
```

### Complexity Analysis
- **addNum**: O(log N) -- at most 3 heap operations, each O(log N).
- **findMedian**: O(1) -- peek at the tops of both heaps.
- **Space**: O(N) total for both heaps.

## Edge Cases

| Edge Case | Expected Behavior | Why It Matters |
|-----------|-------------------|----------------|
| Single element | Median is that element | Base case |
| Two elements | Average of both | Even count case |
| All same values | Median is that value | Heap balancing still works |
| Negative numbers | Works correctly with negation | Negation of negation |
| Alternating large/small | Heaps rebalance correctly | Tests the push-pop-push pattern |
| Large number of calls (50000) | Must be efficient | O(log N) per add is critical |

## Related Problems

| Problem | Relationship | Difficulty |
|---------|-------------|------------|
| Sliding Window Median (LC #480) | Two heaps with removal | Hard |
| Kth Largest Element in Stream (LC #703) | Single heap for streaming | Easy |
| K Closest Points to Origin (LC #973) | Heap for top-k selection | Medium |
| IPO (LC #502) | Two heaps for greedy scheduling | Hard |

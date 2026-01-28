# Find Median from Data Stream - Study Guide

## Problem Summary
> **One-liner**: Design a data structure that supports adding numbers from a stream and finding the median in O(1) time using two heaps.

## Key Intuition

The median splits data into two halves. Maintain a max-heap for the lower half and a min-heap for the upper half. The tops of the heaps are always the two middle elements. By keeping the heaps balanced (sizes differ by at most 1), the median is always accessible at the top(s).

## Approaches Overview

| Approach | addNum | findMedian | Space | Trade-off |
|----------|--------|------------|-------|-----------|
| Sorted List (bisect) | O(N) | O(1) | O(N) | Simple but slow insertion |
| Two Heaps | O(log N) | O(1) | O(N) | Optimal for streaming data |

## Approach 1: Sorted List

### Core Idea
Keep a sorted list. Insert using binary search. Median is the middle element(s).

### Code

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
        return (self.data[mid - 1] + self.data[mid]) / 2.0
```

### Walkthrough
```
addNum(5): data = [5]
addNum(2): data = [2, 5]
findMedian(): n=2, mid=1, return (2+5)/2 = 3.5
addNum(3): data = [2, 3, 5]
findMedian(): n=3, mid=1, return 3.0
```

## Approach 2: Two Heaps (Optimal)

### Core Idea
- `lo` (max-heap, negated): stores the smaller half.
- `hi` (min-heap): stores the larger half.
- Invariant 1: All elements in lo <= all elements in hi.
- Invariant 2: len(lo) == len(hi) or len(lo) == len(hi) + 1.

The three-step insert ensures both invariants:
1. Push to lo.
2. Move lo's max to hi (ensures ordering).
3. If hi is bigger, move hi's min back to lo (ensures sizing).

### Code

```python
import heapq

class MedianFinder:
    def __init__(self):
        self.lo = []  # max-heap (store negated)
        self.hi = []  # min-heap

    def addNum(self, num: int) -> None:
        heapq.heappush(self.lo, -num)
        heapq.heappush(self.hi, -heapq.heappop(self.lo))

        if len(self.hi) > len(self.lo):
            heapq.heappush(self.lo, -heapq.heappop(self.hi))

    def findMedian(self) -> float:
        if len(self.lo) > len(self.hi):
            return -self.lo[0]
        return (-self.lo[0] + self.hi[0]) / 2.0
```

### Walkthrough
```
addNum(1):
  lo: push -1 -> [-1]
  hi: pop lo (-1->1), push 1 -> [1]
  hi bigger? len(hi)=1 > len(lo)=0 -> yes
    lo: pop hi (1), push -1 -> [-1]
  State: lo=[-1], hi=[]

addNum(2):
  lo: push -2 -> [-2, -1]
  hi: pop lo (-2->2), push 2 -> [2]
  hi bigger? len(hi)=1 > len(lo)=1 ([-1]) -> no
  State: lo=[-1], hi=[2]

findMedian():
  len(lo)==len(hi) -> (-(-1) + 2) / 2 = 1.5

addNum(3):
  lo: push -3 -> [-3, -1]
  hi: pop lo (-3->3), push 3 -> [2, 3]
  hi bigger? len(hi)=2 > len(lo)=1 ([-1]) -> yes
    lo: pop hi (2), push -2 -> [-2, -1]
  State: lo=[-2, -1], hi=[3]

findMedian():
  len(lo) > len(hi) -> -(-2) = 2.0
```

## Complexity Analysis

| Operation | Sorted List | Two Heaps |
|-----------|------------|-----------|
| addNum | O(N) | O(log N) |
| findMedian | O(1) | O(1) |
| Space | O(N) | O(N) |
| 50000 operations | ~2.5 billion ops | ~800K ops |

## Common Mistakes

1. **Forgetting negation for max-heap**: Python only has min-heap. You must negate values for the lower half.
2. **Wrong rebalancing logic**: The three-step process (push to lo, move to hi, conditionally move back) is the cleanest. Trying to be clever with if/else often introduces bugs.
3. **Integer division instead of float**: Use `/ 2.0` not `// 2` for the even case.
4. **Off-by-one in size invariant**: lo should have at most 1 more element than hi (or equal). Getting this backwards breaks findMedian.
5. **Not handling the first element**: The three-step process handles this naturally -- no special case needed.

## Interview Tips

- **Draw the two heaps**: Visualize lo as an upside-down heap and hi as a normal heap, with their tops touching at the median.
- **State the invariants clearly**: "lo's max is always <= hi's min" and "lo is either the same size or one bigger than hi."
- **Explain the three-step insert**: This is the most critical part. Practice explaining it concisely.
- **Mention the follow-up**: "If all numbers are in [0, 100], we could use a counting array for O(1) add and O(1) median with a running pointer."
- **Know the sorted list approach as backup**: Show you can start simple and optimize.

## Mnemonics / Memory Aids

- **"Lo-Hi Split"**: lo = lower half (max-heap, negated), hi = higher half (min-heap).
- **"Push-Pop-Balance"**: Push to lo, pop lo to hi, balance if hi is bigger.
- **"Tops tell the tale"**: The median is always at the top(s) of the heaps.
- **"Odd = lo top, Even = average"**: When total count is odd, lo has the extra element.

## Self-Assessment Checklist

- [ ] Can I implement the two-heap solution from scratch?
- [ ] Do I understand the three-step addNum process?
- [ ] Can I state both invariants (ordering and sizing)?
- [ ] Can I trace through addNum and findMedian for 5+ numbers?
- [ ] Do I know why we use negation for the max-heap in Python?
- [ ] Can I explain the time complexity for both operations?
- [ ] Can I discuss the follow-up (numbers in [0,100] range)?

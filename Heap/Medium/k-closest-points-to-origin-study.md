# K Closest Points to Origin - Study Guide

## Problem Summary
> **One-liner**: Find the k points closest to the origin using Euclidean distance, leveraging a heap or quickselect.

## Key Intuition

We need the k smallest items by distance. Instead of sorting everything (O(N log N)), we can maintain a max-heap of size k to track only the k closest seen so far (O(N log k)), or use quickselect to partition around the k-th element (O(N) average). A key optimization is comparing squared distances instead of actual distances, avoiding the sqrt computation.

## Approaches Overview

| Approach | Time | Space | Trade-off |
|----------|------|-------|-----------|
| Sort All | O(N log N) | O(1) | Simple but sorts more than needed |
| Max-Heap of Size K | O(N log k) | O(k) | Better when k is much smaller than N |
| Quickselect | O(N) avg | O(1) | Best average time, worst case O(N^2) |

## Approach 1: Sort All

### Core Idea
Sort the entire array by squared distance and return the first k elements.

### Code

```python
class Solution:
    def kClosest(self, points: list[list[int]], k: int) -> list[list[int]]:
        points.sort(key=lambda p: p[0] ** 2 + p[1] ** 2)
        return points[:k]
```

### Walkthrough
Using `points = [[3,3],[5,-1],[-2,4]], k = 2`:
- Distances: [3,3]=18, [5,-1]=26, [-2,4]=20
- Sorted: [[3,3], [-2,4], [5,-1]]
- Return first 2: [[3,3], [-2,4]]

## Approach 2: Max-Heap of Size K (Optimal)

### Core Idea
Push each point into a max-heap (using negated distance). If the heap exceeds size k, pop the farthest point. After processing all points, the heap holds exactly the k closest.

### Code

```python
import heapq

class Solution:
    def kClosest(self, points: list[list[int]], k: int) -> list[list[int]]:
        max_heap = []

        for x, y in points:
            dist = x * x + y * y
            heapq.heappush(max_heap, (-dist, x, y))

            if len(max_heap) > k:
                heapq.heappop(max_heap)

        return [[x, y] for _, x, y in max_heap]
```

### Walkthrough
Using `points = [[3,3],[5,-1],[-2,4]], k = 2`:
```
Process [3,3]: dist=18, heap=[(-18, 3, 3)]        size=1 <= 2
Process [5,-1]: dist=26, heap=[(-26, 5, -1), (-18, 3, 3)]  size=2 <= 2
Process [-2,4]: dist=20, heap=[(-26, 5, -1), (-18, 3, 3), (-20, -2, 4)]  size=3 > 2
  Pop (-26, 5, -1)  <-- farthest point removed
  heap=[(-20, -2, 4), (-18, 3, 3)]
Result: [[-2, 4], [3, 3]]
```

## Complexity Analysis

| Aspect | Sort | Max-Heap | Quickselect |
|--------|------|----------|-------------|
| Time | O(N log N) | O(N log k) | O(N) average |
| Space | O(1) | O(k) | O(1) |
| Worst case time | O(N log N) | O(N log k) | O(N^2) |
| When to use | k close to N | k much smaller than N | Need best average time |

## Common Mistakes

1. **Using sqrt for distance**: Unnecessary since sqrt preserves ordering. Use `x*x + y*y` instead.
2. **Using a min-heap instead of max-heap**: A min-heap of all N elements works but uses O(N) space. A max-heap of size k uses O(k) space and naturally discards far points.
3. **Forgetting to negate for max-heap**: Python's `heapq` is a min-heap. Negate values to simulate a max-heap.
4. **Not handling the case k == N**: The algorithm works correctly, but you can short-circuit by returning all points.

## Interview Tips

- **Start with sort, then optimize**: Show you can identify the brute force quickly, then present the heap approach.
- **Explain why max-heap not min-heap**: "I use a max-heap of size k so I can efficiently remove the farthest point when a closer one arrives."
- **Mention quickselect**: Even if you implement the heap solution, mentioning quickselect shows depth of knowledge.
- **Discuss squared distances**: Explain that sqrt is monotonic, so comparing squared distances gives the same ordering.
- **Know the heapq API**: `heappush`, `heappop`, and that Python uses min-heap by default.

## Mnemonics / Memory Aids

- **"Negate to Elevate"**: Negate distances to turn Python's min-heap into a max-heap.
- **"K closest = max-heap of k"**: Counterintuitive -- to find the k smallest, use a max-heap so you can discard the largest.
- **"Square, don't root"**: Compare x^2 + y^2 directly, skip the sqrt.

## Self-Assessment Checklist

- [ ] Can I implement the max-heap approach from scratch?
- [ ] Do I understand why we negate distances for the max-heap?
- [ ] Can I explain the time complexity O(N log k)?
- [ ] Do I know the quickselect approach and its average/worst case?
- [ ] Can I explain why squared distance comparison works?
- [ ] Can I trace through the heap operations for a small example?
- [ ] Do I know when to prefer heap vs quickselect vs sort?

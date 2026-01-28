# K Closest Points to Origin

## Problem Information
- **Difficulty**: Medium
- **Category**: Heap / Priority Queue
- **Tags**: Array, Math, Sorting, Heap, Divide and Conquer, Quickselect
- **LeetCode Link**: https://leetcode.com/problems/k-closest-points-to-origin/

## Problem Statement

Given an array of `points` where `points[i] = [xi, yi]` represents a point on the X-Y plane and an integer `k`, return the `k` closest points to the origin `(0, 0)`.

The distance between two points on the X-Y plane is the Euclidean distance (`sqrt(x1^2 + y1^2)`).

You may return the answer in **any order**. The answer is **guaranteed** to be unique (except for the order that it is in).

### Examples

**Example 1:**
```
Input: points = [[1,3],[-2,2]], k = 1
Output: [[-2,2]]
Explanation:
The distance between (1, 3) and the origin is sqrt(10).
The distance between (-2, 2) and the origin is sqrt(8).
Since sqrt(8) < sqrt(10), (-2, 2) is closer to the origin.
We only want the closest k = 1 points from the origin, so the answer is just [[-2,2]].
```

**Example 2:**
```
Input: points = [[3,3],[5,-1],[-2,4]], k = 2
Output: [[3,3],[-2,4]]
```

### Constraints
- `1 <= k <= points.length <= 10^4`
- `-10^4 <= xi, yi <= 10^4`

## Core Concept / Pattern

We need the k smallest elements by distance. We can compare squared distances (avoiding sqrt since it preserves ordering). Three main approaches: sort all, use a max-heap of size k, or use quickselect for average O(N).

## Approach 1: Brute Force (Sort All Points)

### Intuition
Sort all points by their squared distance to the origin, then return the first k.

### Algorithm
1. Compute squared distance for each point: `x^2 + y^2`.
2. Sort the points array by squared distance.
3. Return the first k points.

### Implementation

```python
class Solution:
    def kClosest(self, points: list[list[int]], k: int) -> list[list[int]]:
        points.sort(key=lambda p: p[0] ** 2 + p[1] ** 2)
        return points[:k]
```

### Complexity Analysis
- **Time Complexity**: O(N log N) for sorting all points.
- **Space Complexity**: O(1) extra space if sorting in-place (O(N) for Timsort's internal space).

### Why We Can Do Better
We only need the k smallest, not a fully sorted array. A heap-based approach can do this in O(N log k), and quickselect can achieve O(N) average.

## Approach 2: Optimal Solution (Max-Heap of Size K)

### Intuition
Maintain a max-heap of size k. For each point, push it onto the heap. If the heap exceeds size k, pop the largest element. After processing all points, the heap contains the k closest points. We use a max-heap (negate distances in Python's min-heap) so we always discard the farthest point.

### Algorithm
1. Initialize an empty max-heap.
2. For each point, compute its squared distance.
3. Push `(-distance, point)` onto the heap (negative for max-heap behavior).
4. If heap size exceeds k, pop the top (farthest point).
5. Extract the points from the remaining heap entries.

### Implementation

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

**Alternative (Quickselect for O(N) average):**

```python
import random

class Solution:
    def kClosest(self, points: list[list[int]], k: int) -> list[list[int]]:
        def dist(point):
            return point[0] ** 2 + point[1] ** 2

        def quickselect(left: int, right: int, k: int) -> None:
            if left >= right:
                return

            pivot_idx = random.randint(left, right)
            pivot_dist = dist(points[pivot_idx])
            points[pivot_idx], points[right] = points[right], points[pivot_idx]

            store = left
            for i in range(left, right):
                if dist(points[i]) < pivot_dist:
                    points[i], points[store] = points[store], points[i]
                    store += 1

            points[store], points[right] = points[right], points[store]

            if store == k:
                return
            elif store < k:
                quickselect(store + 1, right, k)
            else:
                quickselect(left, store - 1, k)

        quickselect(0, len(points) - 1, k)
        return points[:k]
```

### Complexity Analysis

**Max-Heap:**
- **Time Complexity**: O(N log k) -- each push/pop operation is O(log k), done N times.
- **Space Complexity**: O(k) for the heap.

**Quickselect:**
- **Time Complexity**: O(N) average, O(N^2) worst case.
- **Space Complexity**: O(1) extra (in-place partitioning).

## Edge Cases

| Edge Case | Expected Behavior | Why It Matters |
|-----------|-------------------|----------------|
| k equals N | Return all points | No filtering needed |
| k equals 1 | Return the single closest point | Minimum heap size |
| All points equidistant | Any k points valid | Answer is not unique but accepted |
| Points at origin `[0,0]` | Distance is 0, always closest | Edge distance calculation |
| Negative coordinates | Squared distance still positive | Math works correctly |

## Related Problems

| Problem | Relationship | Difficulty |
|---------|-------------|------------|
| Kth Largest Element (LC #215) | Same pattern with quickselect/heap | Medium |
| Top K Frequent Elements (LC #347) | Heap for top-k with frequency | Medium |
| Find Median from Data Stream (LC #295) | Heap for streaming data | Hard |
| Sort an Array (LC #912) | Quickselect is related to quicksort | Medium |

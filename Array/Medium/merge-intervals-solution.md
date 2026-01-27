# Merge Intervals

## Problem Information
- **Difficulty**: Medium
- **Category**: Array
- **Tags**: Array, Sorting, Intervals
- **LeetCode Link**: https://leetcode.com/problems/merge-intervals/

## Problem Statement

Given an array of `intervals` where `intervals[i] = [start_i, end_i]`, merge all overlapping intervals, and return an array of the non-overlapping intervals that cover all the intervals in the input.

### Examples

**Example 1:**
```
Input: intervals = [[1,3],[2,6],[8,10],[15,18]]
Output: [[1,6],[8,10],[15,18]]
Explanation: Intervals [1,3] and [2,6] overlap, so merge them into [1,6].
```

**Example 2:**
```
Input: intervals = [[1,4],[4,5]]
Output: [[1,5]]
Explanation: Intervals [1,4] and [4,5] are considered overlapping (they share endpoint 4).
```

### Constraints

- `1 <= intervals.length <= 10^4`
- `intervals[i].length == 2`
- `0 <= start_i <= end_i <= 10^4`

## Core Concept / Pattern

The fundamental insight is that if intervals are sorted by their start time, then overlapping intervals must be adjacent in the sorted order. After sorting, we can perform a single linear scan: for each interval, either it overlaps with the last merged interval (and we extend the merged interval's end) or it does not (and we start a new merged interval). Two intervals overlap when the current interval's start is less than or equal to the previous merged interval's end.

## Approach 1: Brute Force

### Intuition

Repeatedly scan the list of intervals, merging any two that overlap, until no more merges are possible. This is like a fixed-point iteration.

### Algorithm

1. Use a flag to track if any merge happened in the current pass.
2. For each pair of intervals, check if they overlap.
3. If they overlap, merge them and remove one.
4. Repeat until no merges happen.

### Implementation

```python
def merge(intervals: list[list[int]]) -> list[list[int]]:
    intervals = [list(iv) for iv in intervals]  # Copy
    merged = True
    while merged:
        merged = False
        result = []
        used = [False] * len(intervals)
        for i in range(len(intervals)):
            if used[i]:
                continue
            current = intervals[i][:]
            for j in range(i + 1, len(intervals)):
                if used[j]:
                    continue
                if current[0] <= intervals[j][1] and intervals[j][0] <= current[1]:
                    current[0] = min(current[0], intervals[j][0])
                    current[1] = max(current[1], intervals[j][1])
                    used[j] = True
                    merged = True
            result.append(current)
        intervals = result
    return intervals
```

### Complexity Analysis

- **Time Complexity**: O(n^2) in the worst case -- potentially multiple passes with pairwise comparisons.
- **Space Complexity**: O(n) -- for the result array.

### Why We Can Do Better

Sorting the intervals by start time guarantees that overlapping intervals are adjacent, allowing a single linear scan.

## Approach 2: Optimal Solution (Sort + Linear Merge)

### Intuition

Sort intervals by their start time. Then iterate through the sorted intervals. If the current interval overlaps with the last merged interval (i.e., the current start <= last merged end), extend the last merged interval's end. Otherwise, start a new merged interval.

### Algorithm

1. Sort `intervals` by start time.
2. Initialize `merged` with the first interval.
3. For each subsequent interval:
   - If it overlaps with `merged[-1]` (current start <= last end): update `merged[-1][1] = max(merged[-1][1], current end)`.
   - Else: append the current interval to `merged`.
4. Return `merged`.

### Implementation

```python
def merge(intervals: list[list[int]]) -> list[list[int]]:
    intervals.sort(key=lambda x: x[0])

    merged = [intervals[0]]

    for i in range(1, len(intervals)):
        if intervals[i][0] <= merged[-1][1]:
            merged[-1][1] = max(merged[-1][1], intervals[i][1])
        else:
            merged.append(intervals[i])

    return merged
```

### Complexity Analysis

- **Time Complexity**: O(n log n) -- Dominated by sorting. The merge scan is O(n).
- **Space Complexity**: O(n) -- For the output. O(log n) for the sort if done in-place.

## Edge Cases

| Edge Case | Input | Expected Output | Why It Matters |
|---|---|---|---|
| Single interval | `[[1, 3]]` | `[[1, 3]]` | Nothing to merge |
| No overlaps | `[[1,2],[4,5],[7,8]]` | `[[1,2],[4,5],[7,8]]` | All intervals are disjoint |
| All overlap | `[[1,4],[2,5],[3,6]]` | `[[1,6]]` | Everything merges into one |
| Touching endpoints | `[[1,2],[2,3]]` | `[[1,3]]` | Endpoints that touch should merge |
| Nested intervals | `[[1,10],[2,3],[4,5]]` | `[[1,10]]` | Inner intervals are absorbed |
| Unsorted input | `[[3,4],[1,2]]` | `[[1,2],[3,4]]` | Must sort first |

## Related Problems

| Problem | Difficulty | How It Relates |
|---|---|---|
| [Insert Interval](https://leetcode.com/problems/insert-interval/) | Medium | Insert then merge (sorted input) |
| [Non-overlapping Intervals](https://leetcode.com/problems/non-overlapping-intervals/) | Medium | Remove minimum intervals to eliminate overlaps |
| [Meeting Rooms](https://leetcode.com/problems/meeting-rooms/) | Easy | Check if any intervals overlap |
| [Meeting Rooms II](https://leetcode.com/problems/meeting-rooms-ii/) | Medium | Count maximum concurrent overlaps |
| [Interval List Intersections](https://leetcode.com/problems/interval-list-intersections/) | Medium | Two-pointer on two sorted interval lists |

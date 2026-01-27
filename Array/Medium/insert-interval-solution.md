# Insert Interval

## Problem Information
- **Difficulty**: Medium
- **Category**: Array
- **Tags**: Array, Intervals
- **LeetCode Link**: https://leetcode.com/problems/insert-interval/

## Problem Statement

You are given an array of non-overlapping intervals `intervals` where `intervals[i] = [start_i, end_i]` represent the start and end of the `i`th interval and `intervals` is sorted in ascending order by `start_i`. You are also given an interval `newInterval = [start, end]` that represents the start and end of another interval.

Insert `newInterval` into `intervals` such that `intervals` is still sorted in ascending order by `start_i` and `intervals` still does not have any overlapping intervals (merge overlapping intervals if necessary).

Return `intervals` after the insertion.

**Note**: You don't need to modify `intervals` in-place. You can make a new array and return it.

### Examples

**Example 1:**
```
Input: intervals = [[1,3],[6,9]], newInterval = [2,5]
Output: [[1,5],[6,9]]
Explanation: The new interval [2,5] overlaps with [1,3], so they merge into [1,5].
```

**Example 2:**
```
Input: intervals = [[1,2],[3,5],[6,7],[8,10],[12,16]], newInterval = [4,8]
Output: [[1,2],[3,10],[12,16]]
Explanation: The new interval [4,8] overlaps with [3,5],[6,7],[8,10], merging into [3,10].
```

### Constraints

- `0 <= intervals.length <= 10^4`
- `intervals[i].length == 2`
- `0 <= start_i <= end_i <= 10^5`
- `intervals` is sorted by `start_i` in ascending order.
- `newInterval.length == 2`
- `0 <= start <= end <= 10^5`

## Core Concept / Pattern

The intervals are already sorted and non-overlapping. When inserting a new interval, we can divide existing intervals into three groups: (1) intervals that come entirely before the new interval, (2) intervals that overlap with the new interval and need to be merged, and (3) intervals that come entirely after the new interval. The algorithm processes these three groups in order using a linear scan.

Two intervals `[a, b]` and `[c, d]` overlap if and only if `a <= d` and `c <= b`. When merging, the result is `[min(a, c), max(b, d)]`.

## Approach 1: Brute Force

### Intuition

Insert the new interval into the list, re-sort, then merge all overlapping intervals using the standard merge-intervals algorithm.

### Algorithm

1. Append `newInterval` to `intervals`.
2. Sort by start time.
3. Merge overlapping intervals by iterating through and combining overlaps.

### Implementation

```python
def insert(intervals: list[list[int]], newInterval: list[int]) -> list[list[int]]:
    intervals.append(newInterval)
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

- **Time Complexity**: O(n log n) -- Due to sorting.
- **Space Complexity**: O(n) -- For the merged result.

### Why We Can Do Better

The intervals are already sorted. We do not need to re-sort. We can exploit the sorted order to process intervals in a single linear pass.

## Approach 2: Optimal Solution (Linear Scan)

### Intuition

Since intervals are sorted, we process them in three phases:
1. **Before**: Add all intervals that end before the new interval starts (no overlap).
2. **Overlap**: Merge all intervals that overlap with the new interval into one combined interval.
3. **After**: Add all remaining intervals that start after the merged interval ends.

Two intervals overlap when the start of one is less than or equal to the end of the other.

### Algorithm

1. Initialize an empty result list.
2. Iterate through intervals:
   - If `intervals[i][1] < newInterval[0]`: interval is entirely before -- add to result.
   - Else if `intervals[i][0] <= newInterval[1]`: interval overlaps -- merge into newInterval.
   - Else: interval is entirely after -- stop merging.
3. Add the (possibly merged) newInterval to result.
4. Add all remaining intervals.

### Implementation

```python
def insert(intervals: list[list[int]], newInterval: list[int]) -> list[list[int]]:
    result = []
    i = 0
    n = len(intervals)

    # Phase 1: Add all intervals that come before newInterval
    while i < n and intervals[i][1] < newInterval[0]:
        result.append(intervals[i])
        i += 1

    # Phase 2: Merge all overlapping intervals with newInterval
    while i < n and intervals[i][0] <= newInterval[1]:
        newInterval[0] = min(newInterval[0], intervals[i][0])
        newInterval[1] = max(newInterval[1], intervals[i][1])
        i += 1
    result.append(newInterval)

    # Phase 3: Add all intervals that come after
    while i < n:
        result.append(intervals[i])
        i += 1

    return result
```

### Complexity Analysis

- **Time Complexity**: O(n) -- Each interval is processed exactly once.
- **Space Complexity**: O(n) -- For the output array (O(1) extra space besides the output).

## Edge Cases

| Edge Case | Input | Expected Output | Why It Matters |
|---|---|---|---|
| Empty intervals | `[], [2, 5]` | `[[2, 5]]` | No existing intervals |
| No overlap, insert at start | `[[3,5]], [1,2]` | `[[1,2],[3,5]]` | New interval is first |
| No overlap, insert at end | `[[1,2]], [3,5]` | `[[1,2],[3,5]]` | New interval is last |
| Complete overlap | `[[1,5]], [2,3]` | `[[1,5]]` | New interval inside existing |
| Merge all | `[[1,2],[3,4],[5,6]], [1,6]` | `[[1,6]]` | New interval covers everything |
| Adjacent intervals | `[[1,2],[4,5]], [2,4]` | `[[1,5]]` | Touching boundaries merge |

## Related Problems

| Problem | Difficulty | How It Relates |
|---|---|---|
| [Merge Intervals](https://leetcode.com/problems/merge-intervals/) | Medium | Core merge logic is reused |
| [Non-overlapping Intervals](https://leetcode.com/problems/non-overlapping-intervals/) | Medium | Interval scheduling / greedy |
| [Meeting Rooms](https://leetcode.com/problems/meeting-rooms/) | Easy | Overlap detection in intervals |
| [Meeting Rooms II](https://leetcode.com/problems/meeting-rooms-ii/) | Medium | Counting concurrent overlaps |
| [Interval List Intersections](https://leetcode.com/problems/interval-list-intersections/) | Medium | Two-pointer on sorted intervals |

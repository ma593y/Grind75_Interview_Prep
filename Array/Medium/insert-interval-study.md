# Insert Interval - Study Guide

## Problem Summary

> **One-liner**: Insert a new interval into a sorted list of non-overlapping intervals, merging any overlaps that result.

## Key Intuition

Since the intervals are already sorted and non-overlapping, the problem naturally breaks into three phases: intervals entirely before the new interval (no overlap, just copy them), intervals that overlap with the new interval (merge them all into one), and intervals entirely after (no overlap, just copy them). The overlap condition is simple: two intervals `[a,b]` and `[c,d]` overlap when `a <= d` AND `c <= b`.

## Approaches Overview

| Approach | Time | Space | Pros | Cons |
|---|---|---|---|---|
| Brute Force (sort + merge) | O(n log n) | O(n) | Reuses merge-intervals logic | Wastes sorted order |
| Linear Scan (3 phases) | O(n) | O(n) | Optimal, clean structure | Slightly more code |

## Approach 1: Brute Force (Sort + Merge)

### Core Idea

Append the new interval, re-sort, then merge overlapping intervals. This ignores the fact that the list is already sorted.

### Code

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

### Walkthrough

Given `intervals = [[1,3],[6,9]]`, `newInterval = [2,5]`:

1. After append: `[[1,3],[6,9],[2,5]]`
2. After sort: `[[1,3],[2,5],[6,9]]`
3. Merge: `[1,3]` and `[2,5]` overlap (2 <= 3) -> `[1,5]`. `[6,9]` does not overlap with `[1,5]`.
4. Result: `[[1,5],[6,9]]`

## Approach 2: Linear Scan (Optimal)

### Core Idea

Process intervals in three phases using the sorted order:
- **Phase 1**: Copy intervals that end before the new interval starts.
- **Phase 2**: Merge intervals that overlap with the new interval.
- **Phase 3**: Copy intervals that start after the new interval ends.

### Code

```python
def insert(intervals: list[list[int]], newInterval: list[int]) -> list[list[int]]:
    result = []
    i = 0
    n = len(intervals)

    # Phase 1: Before
    while i < n and intervals[i][1] < newInterval[0]:
        result.append(intervals[i])
        i += 1

    # Phase 2: Overlap -- merge
    while i < n and intervals[i][0] <= newInterval[1]:
        newInterval[0] = min(newInterval[0], intervals[i][0])
        newInterval[1] = max(newInterval[1], intervals[i][1])
        i += 1
    result.append(newInterval)

    # Phase 3: After
    while i < n:
        result.append(intervals[i])
        i += 1

    return result
```

### Walkthrough

Given `intervals = [[1,2],[3,5],[6,7],[8,10],[12,16]]`, `newInterval = [4,8]`:

**Phase 1** (end < 4):
- `[1,2]`: end=2 < 4, add to result. Result: `[[1,2]]`

**Phase 2** (start <= 8):
- `[3,5]`: start=3 <= 8, merge: newInterval = `[min(4,3), max(8,5)]` = `[3,8]`
- `[6,7]`: start=6 <= 8, merge: newInterval = `[min(3,6), max(8,7)]` = `[3,8]`
- `[8,10]`: start=8 <= 8, merge: newInterval = `[min(3,8), max(8,10)]` = `[3,10]`
- Add merged `[3,10]` to result. Result: `[[1,2],[3,10]]`

**Phase 3** (remaining):
- `[12,16]`: add to result. Result: `[[1,2],[3,10],[12,16]]`

## Complexity Analysis

| Approach | Time | Space | Notes |
|---|---|---|---|
| Sort + Merge | O(n log n) | O(n) | Does not exploit sorted input |
| Linear Scan | O(n) | O(n) | Optimal single-pass |

## Common Mistakes

1. **Wrong overlap condition**: Using `<` instead of `<=` for the merge phase. `[1,2]` and `[2,3]` are considered overlapping (they share endpoint 2).
2. **Forgetting to add newInterval**: After the merge phase, you must add the (possibly merged) newInterval to the result.
3. **Modifying intervals in-place**: The problem says you can return a new array. Modifying the input can lead to bugs.
4. **Not handling empty intervals list**: When `intervals` is empty, just return `[newInterval]`.
5. **Boundary confusion**: Phase 1 checks `intervals[i][1] < newInterval[0]` (strictly less than), while Phase 2 checks `intervals[i][0] <= newInterval[1]` (less than or equal).

## Interview Tips

- **Draw it out**: Interval problems benefit enormously from visual diagrams on a whiteboard. Draw the intervals on a number line.
- **State the three phases clearly**: "Before, Overlap, After" is a clean mental framework that interviewers appreciate.
- **Know the overlap condition**: Two intervals `[a,b]` and `[c,d]` overlap iff `a <= d` and `c <= b`. This is fundamental for all interval problems.
- **Handle edge cases explicitly**: Empty input, no overlap, complete overlap, and adjacent intervals.

## Mnemonics / Memory Aids

- **"Before, Merge, After"**: Three phases, three while loops. Each has one clear condition.
- **"End before start = no overlap"**: If one interval's end is strictly before another's start, they do not overlap.
- **"Min start, max end"**: When merging, the merged interval has the minimum of the two starts and the maximum of the two ends.

## Self-Assessment Checklist

- [ ] Can I implement the three-phase linear scan from memory?
- [ ] Can I state the overlap condition for two intervals without hesitation?
- [ ] Can I trace through an example with multiple merges?
- [ ] Do I handle the case where newInterval does not overlap with anything?
- [ ] Do I handle the case where newInterval comes before all existing intervals?
- [ ] Can I explain why Phase 1 uses strict `<` but Phase 2 uses `<=`?

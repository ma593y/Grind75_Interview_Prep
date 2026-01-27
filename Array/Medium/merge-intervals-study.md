# Merge Intervals - Study Guide

## Problem Summary

> **One-liner**: Merge all overlapping intervals in a collection and return the resulting non-overlapping intervals.

## Key Intuition

Sorting by start time is the crucial first step. Once sorted, overlapping intervals must be consecutive, so a single left-to-right scan suffices. At each step, if the current interval's start is within the last merged interval's range (start <= last end), we extend the merged interval. Otherwise, we begin a new group. This sort-then-scan pattern is the foundation of virtually all interval merging problems.

## Approaches Overview

| Approach | Time | Space | Pros | Cons |
|---|---|---|---|---|
| Brute Force (repeated merging) | O(n^2) | O(n) | No sorting needed | Very slow |
| Sort + Linear Merge | O(n log n) | O(n) | Optimal, clean | Must sort first |

## Approach 1: Brute Force

### Core Idea

Repeatedly iterate through all pairs of intervals, merging any two that overlap, until no more merges are possible.

### Code

```python
def merge(intervals: list[list[int]]) -> list[list[int]]:
    result = [iv[:] for iv in intervals]
    changed = True
    while changed:
        changed = False
        new_result = []
        used = set()
        for i in range(len(result)):
            if i in used:
                continue
            current = result[i][:]
            for j in range(i + 1, len(result)):
                if j in used:
                    continue
                if current[0] <= result[j][1] and result[j][0] <= current[1]:
                    current = [min(current[0], result[j][0]), max(current[1], result[j][1])]
                    used.add(j)
                    changed = True
            new_result.append(current)
        result = new_result
    return result
```

### Walkthrough

Given `intervals = [[1,3],[2,6],[8,10],[15,18]]`:
- Pass 1: `[1,3]` and `[2,6]` overlap -> merge to `[1,6]`. Result: `[[1,6],[8,10],[15,18]]`.
- Pass 2: No more overlaps. Done.

## Approach 2: Sort + Linear Merge (Optimal)

### Core Idea

Sort by start time. Scan left to right, merging each interval into the last merged interval if they overlap.

### Code

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

### Walkthrough

Given `intervals = [[1,3],[2,6],[8,10],[15,18]]`:

After sorting (already sorted): `[[1,3],[2,6],[8,10],[15,18]]`

| Step | Current Interval | merged[-1] | Overlap? | Action | merged |
|---|---|---|---|---|---|
| Init | -- | -- | -- | Add [1,3] | `[[1,3]]` |
| 1 | [2,6] | [1,3] | 2 <= 3: Yes | Extend to [1,6] | `[[1,6]]` |
| 2 | [8,10] | [1,6] | 8 <= 6: No | Add [8,10] | `[[1,6],[8,10]]` |
| 3 | [15,18] | [8,10] | 15 <= 10: No | Add [15,18] | `[[1,6],[8,10],[15,18]]` |

Result: `[[1,6],[8,10],[15,18]]`

## Complexity Analysis

| Approach | Time | Space | Notes |
|---|---|---|---|
| Brute Force | O(n^2) | O(n) | Multiple passes |
| Sort + Merge | O(n log n) | O(n) | Sorting dominates |

## Common Mistakes

1. **Forgetting to sort**: The algorithm only works on sorted intervals. Unsorted input produces wrong results.
2. **Using start instead of end for overlap check**: The condition is `current.start <= last_merged.end`, not comparing starts.
3. **Not using max for end extension**: When merging, the new end must be `max(merged[-1][1], intervals[i][1])`, not just `intervals[i][1]`. The existing merged interval might extend further.
4. **Treating touching endpoints as non-overlapping**: `[1,2]` and `[2,3]` should merge to `[1,3]`. Use `<=` not `<`.
5. **Modifying the input array**: Always safer to work with a copy or build a new result.

## Interview Tips

- **Sort first, always**: For any interval problem, sorting by start time is almost always the first step. State this upfront.
- **Clarify overlap definition**: Ask whether touching endpoints (like `[1,2]` and `[2,3]`) count as overlapping. For this problem, they do.
- **Walk through an example**: Use 3-4 intervals on a number line drawing to demonstrate the merge process.
- **Know the pattern family**: Merge Intervals, Insert Interval, Meeting Rooms, and Non-overlapping Intervals all share the sort-then-scan pattern.
- **Discuss the sort cost**: The merge scan is O(n) but sorting is O(n log n), making the overall complexity O(n log n). This is optimal for comparison-based approaches since we need the sorted order.

## Mnemonics / Memory Aids

- **"Sort, Scan, Stretch"**: Sort by start, scan left-to-right, stretch the last interval when there is overlap.
- **"Start meets End"**: Overlap happens when the current start meets or is before the previous end.
- **"Max the end"**: When merging, always take the maximum end value -- the current interval might end before the existing merged one.

## Self-Assessment Checklist

- [ ] Can I implement sort + merge from memory?
- [ ] Can I state the overlap condition for two intervals?
- [ ] Can I handle touching endpoints correctly?
- [ ] Can I handle nested intervals (one completely inside another)?
- [ ] Do I know why sorting by start time guarantees adjacent overlaps?
- [ ] Can I extend this to count the maximum number of concurrent intervals (Meeting Rooms II)?

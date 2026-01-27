# Contains Duplicate - Study Guide

## Problem Summary

> **One-liner**: Determine if any value appears at least twice in an integer array.

## Key Intuition

Duplicate detection is a classic use case for hash sets. A set provides O(1) average-case membership testing, so we can iterate through the array once, checking each element against the set of previously seen values. The moment we find an element already in the set, we know there is a duplicate. This transforms an O(n^2) brute-force comparison into an O(n) single-pass algorithm.

## Approaches Overview

| Approach | Time | Space | Pros | Cons |
|---|---|---|---|---|
| Brute Force (nested loops) | O(n^2) | O(1) | No extra space | Far too slow for large inputs |
| Sorting | O(n log n) | O(1)* | No extra space if in-place | Modifies original array |
| Hash Set | O(n) | O(n) | Optimal time, simple code | Uses extra space |

*\*O(1) extra space only if sorting is done in-place.*

## Approach 1: Brute Force

### Core Idea

Compare every pair of elements. If any two match, return `True`.

### Code

```python
def containsDuplicate(nums: list[int]) -> bool:
    n = len(nums)
    for i in range(n):
        for j in range(i + 1, n):
            if nums[i] == nums[j]:
                return True
    return False
```

### Walkthrough

Given `nums = [1, 2, 3, 1]`:

1. `i=0, j=1`: Compare `1` vs `2` -- not equal.
2. `i=0, j=2`: Compare `1` vs `3` -- not equal.
3. `i=0, j=3`: Compare `1` vs `1` -- **equal!** Return `True`.

We found the duplicate without even finishing the first outer iteration.

## Approach 2: Hash Set (Optimal)

### Core Idea

Maintain a set of elements seen so far. For each element, check if it is already in the set. If yes, we found a duplicate. If no, add it and move on.

### Code

```python
def containsDuplicate(nums: list[int]) -> bool:
    seen = set()
    for num in nums:
        if num in seen:
            return True
        seen.add(num)
    return False
```

### Walkthrough

Given `nums = [1, 2, 3, 1]`:

| Step | num | seen (before) | In set? | Action |
|---|---|---|---|---|
| 1 | 1 | `{}` | No | Add 1 -> `{1}` |
| 2 | 2 | `{1}` | No | Add 2 -> `{1, 2}` |
| 3 | 3 | `{1, 2}` | No | Add 3 -> `{1, 2, 3}` |
| 4 | 1 | `{1, 2, 3}` | **Yes** | Return `True` |

## Approach 3: Sorting

### Core Idea

Sort the array so duplicates become adjacent, then scan consecutive pairs.

### Code

```python
def containsDuplicate(nums: list[int]) -> bool:
    nums.sort()
    for i in range(1, len(nums)):
        if nums[i] == nums[i - 1]:
            return True
    return False
```

### Walkthrough

Given `nums = [1, 2, 3, 1]`:

1. After sorting: `[1, 1, 2, 3]`.
2. Compare index 0 and 1: `1 == 1` -- **match!** Return `True`.

## Complexity Analysis

| Approach | Time | Space | Notes |
|---|---|---|---|
| Brute Force | O(n^2) | O(1) | n*(n-1)/2 comparisons |
| Sorting | O(n log n) | O(1) | Modifies input array |
| Hash Set | O(n) | O(n) | Best time complexity |

## Common Mistakes

1. **Forgetting early return**: Not returning as soon as a duplicate is found, leading to unnecessary work.
2. **Using a list instead of a set**: Using `if num in list` is O(n), making the overall approach O(n^2) despite looking like the hash set solution.
3. **Off-by-one in sorting approach**: Starting the comparison loop at index 0 instead of index 1, which causes an index-out-of-bounds when checking `nums[i-1]`.
4. **Not considering the one-liner**: `len(nums) != len(set(nums))` is the most Pythonic solution but always processes the entire array, even if a duplicate is found early.

## Interview Tips

- **Start with the brute force**: Mention the O(n^2) approach first to show you understand the baseline, then optimize.
- **Discuss trade-offs**: The sorting approach uses O(1) extra space but modifies the input and is O(n log n). The hash set approach is O(n) time but uses O(n) space. Interviewers love hearing about space-time trade-offs.
- **Ask clarifying questions**: Can we modify the input array? Are there constraints on space usage? These questions show maturity.
- **Mention the one-liner**: After presenting the hash set solution, mention `len(nums) != len(set(nums))` as a Pythonic alternative, but note it does not short-circuit.

## Mnemonics / Memory Aids

- **"Set = Seen"**: Think of a set as your memory of what you have already seen. If you see it again, it is a duplicate.
- **"Sort brings neighbors together"**: After sorting, duplicates must be next to each other, like neighbors in a sorted phone book with the same last name.

## Self-Assessment Checklist

- [ ] Can I explain why a hash set gives O(1) lookup on average?
- [ ] Can I implement the hash set solution without looking at notes?
- [ ] Can I articulate the time-space trade-off between sorting and hash set approaches?
- [ ] Do I know when the sorting approach might be preferred (memory-constrained environments)?
- [ ] Can I handle edge cases (single element, all duplicates, empty array)?
- [ ] Can I extend this to "Contains Duplicate II" (with a sliding window constraint)?

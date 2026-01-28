# Sort Colors - Study Guide

## Problem Summary
- **Difficulty**: Medium
- **Category**: Array
- **Tags**: Array, Two Pointers, Sorting
- **LeetCode Link**: https://leetcode.com/problems/sort-colors/

> **One-liner**: Sort an array of 0s, 1s, and 2s in-place using the Dutch National Flag algorithm.

## Key Intuition
Use three pointers to partition the array into three regions: 0s on the left, 1s in the middle, 2s on the right. The `mid` pointer scans through, swapping elements to their correct region. The critical insight is: when swapping with `high`, don't advance `mid` because the swapped element hasn't been examined yet.

## Approaches Overview

| Approach | Time | Space | Trade-off |
|----------|------|-------|-----------|
| Counting Sort | O(n) | O(1) | Two passes, simple |
| Dutch National Flag | O(n) | O(1) | One pass, trickier |

## Approach 1: Counting Sort

### Core Idea
Count each color, then overwrite the array.

### Code
```python
def sortColors(self, nums: list[int]) -> None:
    count = [0, 0, 0]
    for num in nums:
        count[num] += 1

    idx = 0
    for color in range(3):
        for _ in range(count[color]):
            nums[idx] = color
            idx += 1
```

### Walkthrough
`[2,0,2,1,1,0]` → count = [2,2,2] → write [0,0,1,1,2,2]

## Approach 2: Dutch National Flag (Optimal)

### Core Idea
Three pointers: `low` marks end of 0s, `high` marks start of 2s, `mid` scans and swaps.

### Code
```python
def sortColors(self, nums: list[int]) -> None:
    low, mid, high = 0, 0, len(nums) - 1

    while mid <= high:
        if nums[mid] == 0:
            nums[low], nums[mid] = nums[mid], nums[low]
            low += 1
            mid += 1
        elif nums[mid] == 1:
            mid += 1
        else:  # nums[mid] == 2
            nums[mid], nums[high] = nums[high], nums[mid]
            high -= 1
            # Don't increment mid!
```

### Walkthrough
`[2,0,2,1,1,0]`:
1. mid=0, val=2: swap with high=5 → [0,0,2,1,1,2], high=4
2. mid=0, val=0: swap with low=0 (no change), low=1, mid=1
3. mid=1, val=0: swap with low=1 (no change), low=2, mid=2
4. mid=2, val=2: swap with high=4 → [0,0,1,1,2,2], high=3
5. mid=2, val=1: mid=3
6. mid=3, val=1: mid=4
7. mid=4 > high=3: done!

## Complexity Analysis
- **Time**: O(n) -- single pass through array
- **Space**: O(1) -- only three pointers

## Common Mistakes
1. **Incrementing mid after swapping with high**: The element swapped from high hasn't been examined yet!
2. **Wrong loop condition**: Must be `mid <= high`, not `mid < high`
3. **Off-by-one with pointers**: `low` points to first non-0, `high` points to last non-2

## Interview Tips
- **How to communicate**: "This is the Dutch National Flag problem. I'll use three pointers to partition into three regions in one pass."
- **Clarifying questions to ask**: "Should I sort in-place? Can I use extra space?"
- **Optimization path**: Start with counting sort (two-pass), then optimize to one-pass
- **Follow-up variations**: "What if there are k colors?" (Use k-1 pointers or counting sort)

## Mnemonics / Memory Aids
**"Low Mid High"**:
- `low`: everything before is 0
- `mid`: current scanner
- `high`: everything after is 2

**"Don't move mid when swapping right"**: The element from high is unknown, must check it.

## Self-Assessment Checklist
- [ ] Can I identify this problem type within 2 minutes?
- [ ] Can I code the optimal solution without looking at notes?
- [ ] Can I explain the time/space complexity clearly?
- [ ] Can I handle all edge cases?
- [ ] Can I discuss trade-offs between approaches?

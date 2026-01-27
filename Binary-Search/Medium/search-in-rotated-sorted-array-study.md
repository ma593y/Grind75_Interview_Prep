# Search in Rotated Sorted Array - Study Guide

## Problem Summary
- **Difficulty**: Medium
- **Category**: Binary Search
- **Tags**: Array, Binary Search
- **LeetCode Link**: https://leetcode.com/problems/search-in-rotated-sorted-array/

> **One-liner**: Find a target value in a sorted array that has been rotated at an unknown pivot, in O(log n) time.

## Key Intuition
Even after rotation, at least one half of the array (split at mid) is always sorted. We can identify the sorted half by comparing `nums[left]` with `nums[mid]`, then check if the target lies within that sorted range. This lets us decide which half to discard, preserving the O(log n) binary search guarantee.

## Approaches Overview
| Approach | Time | Space | Trade-off |
|----------|------|-------|-----------|
| Linear Scan | O(n) | O(1) | Simple but ignores sorted structure |
| Modified Binary Search | O(log n) | O(1) | Optimal -- exploits partial sorted order |

## Approach 1: Linear Scan
### Core Idea
Iterate through every element and check if it matches the target.

### Code
```python
def search(nums: list[int], target: int) -> int:
    for i in range(len(nums)):
        if nums[i] == target:
            return i
    return -1
```

### Walkthrough
For `nums = [4, 5, 6, 7, 0, 1, 2]`, `target = 0`:
- i=0: 4 != 0, continue
- i=1: 5 != 0, continue
- i=2: 6 != 0, continue
- i=3: 7 != 0, continue
- i=4: 0 == 0, return 4

## Approach 2: Modified Binary Search (Optimal)
### Core Idea
At each step, determine which half is sorted. If the target falls in the sorted half's range, search there. Otherwise, search the other half.

### Code
```python
def search(nums: list[int], target: int) -> int:
    left, right = 0, len(nums) - 1

    while left <= right:
        mid = left + (right - left) // 2

        if nums[mid] == target:
            return mid

        # Left half is sorted
        if nums[left] <= nums[mid]:
            if nums[left] <= target < nums[mid]:
                right = mid - 1
            else:
                left = mid + 1
        # Right half is sorted
        else:
            if nums[mid] < target <= nums[right]:
                left = mid + 1
            else:
                right = mid - 1

    return -1
```

### Walkthrough
For `nums = [4, 5, 6, 7, 0, 1, 2]`, `target = 0`:
- left=0, right=6, mid=3: nums[3]=7. Left half [4,5,6,7] is sorted (4 <= 7). Target 0 is NOT in [4,7), so left=4.
- left=4, right=6, mid=5: nums[5]=1. Left half [0,1] is sorted (0 <= 1). Target 0 is in [0,1), so right=4.
- left=4, right=4, mid=4: nums[4]=0 == target. Return 4.

## Complexity Analysis
| Approach | Time | Space | Explanation |
|----------|------|-------|-------------|
| Linear Scan | O(n) | O(1) | Checks every element |
| Modified Binary Search | O(log n) | O(1) | Halves search space each step |

## Common Mistakes
1. **Using `nums[left] < nums[mid]` instead of `<=`**: When `left == mid` (two-element subarray), you need `<=` to correctly identify the left half as sorted.
2. **Wrong range checks**: Using `<=` on both ends of the range check (e.g., `nums[left] <= target <= nums[mid]`) causes issues when `target == nums[mid]`, which is already handled above.
3. **Forgetting the array might not be rotated**: A fully sorted array is a valid input (rotated by 0).
4. **Off-by-one in boundary conditions**: Mixing inclusive and exclusive bounds when checking target range.

## Interview Tips
- **How to communicate**: Start by explaining that rotation preserves partial sorted order. Mention that at any split point, one half must be fully sorted.
- **Clarifying questions to ask**: Are all elements unique? Can the array be unrotated? What should I return if the target is not found?
- **Optimization path**: "Linear scan is O(n), but the partial sorted structure lets us apply binary search for O(log n)."
- **Follow-up variations**: What if duplicates are allowed (LC #81)? What if you need to find the minimum element instead (LC #153)?

## Mnemonics / Memory Aids
- **"One half always sorted"**: At any midpoint in a rotated sorted array, one side is guaranteed to be in order.
- **"Check sorted side first"**: Identify the sorted half, check if target is in its range, and eliminate accordingly.
- **"LEFT-less-equal-MID means left sorted"**: `nums[left] <= nums[mid]` tells you the left half has no rotation break.

## Self-Assessment Checklist
- [ ] Can I identify this problem type within 2 minutes?
- [ ] Can I code the optimal solution without looking at notes?
- [ ] Can I explain why one half is always sorted?
- [ ] Can I handle edge cases (no rotation, single element, two elements)?
- [ ] Can I discuss the follow-up with duplicates?

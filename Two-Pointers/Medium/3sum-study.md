# 3Sum - Study Guide

## Problem Summary
> **One-liner**: Find all unique triplets in an array that sum to zero, avoiding duplicate results.

## Key Intuition

Sorting transforms 3Sum into a series of 2Sum-on-sorted-array problems. Fix one element, then use two pointers converging from both ends to find complementary pairs. Duplicate skipping at all three positions ensures unique triplets.

## Approaches Overview

| Approach | Time | Space | Key Idea |
|----------|------|-------|----------|
| Brute Force (3 loops) | O(n^3) | O(n) | Check all triples, use set for dedup |
| Hash Set (fix one, hash two) | O(n^2) | O(n) | Fix one, use set to find complement |
| Sort + Two Pointers | O(n^2) | O(1)* | Sort, fix one, two-pointer for pair |

*Excluding output and sorting stack space.

## Approach 1: Brute Force

### Core Idea
Three nested loops check every possible triplet. A set of sorted tuples prevents duplicate triplets.

### Code

```python
class Solution:
    def threeSum(self, nums: list[int]) -> list[list[int]]:
        result = set()
        n = len(nums)
        for i in range(n):
            for j in range(i + 1, n):
                for k in range(j + 1, n):
                    if nums[i] + nums[j] + nums[k] == 0:
                        result.add(tuple(sorted([nums[i], nums[j], nums[k]])))
        return [list(t) for t in result]
```

### Walkthrough
For `nums = [-1, 0, 1, 2, -1, -4]`:
- Checks all C(6,3) = 20 triplets
- Finds (-1,0,1), (-1,2,-1), (0,1,-1) -- after sorting and deduplication: `{(-1,-1,2), (-1,0,1)}`

## Approach 2: Sort + Two Pointers (Optimal)

### Core Idea
Sort the array. For each index `i`, treat `nums[i]` as the fixed element and find pairs in `nums[i+1..n-1]` that sum to `-nums[i]` using two converging pointers. Skip duplicates at every level.

### Code

```python
class Solution:
    def threeSum(self, nums: list[int]) -> list[list[int]]:
        nums.sort()
        result = []
        n = len(nums)

        for i in range(n - 2):
            if i > 0 and nums[i] == nums[i - 1]:
                continue
            if nums[i] > 0:
                break

            left, right = i + 1, n - 1
            while left < right:
                total = nums[i] + nums[left] + nums[right]
                if total < 0:
                    left += 1
                elif total > 0:
                    right -= 1
                else:
                    result.append([nums[i], nums[left], nums[right]])
                    while left < right and nums[left] == nums[left + 1]:
                        left += 1
                    while left < right and nums[right] == nums[right - 1]:
                        right -= 1
                    left += 1
                    right -= 1

        return result
```

### Walkthrough
For `nums = [-1, 0, 1, 2, -1, -4]`:
1. Sort: `[-4, -1, -1, 0, 1, 2]`
2. i=0, nums[i]=-4, target=4, left=1, right=5
   - -1+2=1 < 4, left++
   - -1+2=1 < 4, left++
   - 0+2=2 < 4, left++
   - 1+2=3 < 4, left++
   - left >= right, done
3. i=1, nums[i]=-1, target=1, left=2, right=5
   - -1+2=1 == 1 -> found [-1,-1,2], skip dups, left=3, right=4
   - 0+1=1 == 1 -> found [-1,0,1], skip dups, left=4, right=3
   - left >= right, done
4. i=2, nums[i]=-1, same as nums[1], skip
5. i=3, nums[i]=0 > 0? No. left=4, right=5
   - 1+2=3 > 0, right--
   - left >= right, done
6. Result: `[[-1,-1,2], [-1,0,1]]`

## Complexity Analysis

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Brute Force | O(n^3) | O(n) | Set for deduplication |
| Sort + Two Pointers | O(n^2) | O(1)* | Sorting O(n log n) dominated by O(n^2) |

## Common Mistakes

1. **Not sorting first** -- the two-pointer technique requires a sorted array; without sorting, you cannot determine which pointer to move.
2. **Only skipping duplicates for `i` but not for `left`/`right`** -- all three positions need duplicate skipping to avoid repeated triplets.
3. **Off-by-one in duplicate skipping** -- the outer loop should check `nums[i] == nums[i-1]` (not `nums[i+1]`) so the first occurrence is still processed.
4. **Forgetting `left += 1` and `right -= 1` after recording a triplet** -- without advancing both pointers, you get an infinite loop.
5. **Missing the early termination** -- if `nums[i] > 0` after sorting, no valid triplet can be found (all remaining values are positive).

## Interview Tips

- **Connect to 2Sum**: Explicitly state that 3Sum reduces to n instances of 2Sum. This shows structured problem decomposition.
- **Explain duplicate skipping carefully**: This is the trickiest part. Walk through a concrete example showing what happens without it.
- **Mention the early break**: `if nums[i] > 0: break` is a nice optimization that shows attention to detail.
- **Know the time complexity proof**: Outer loop O(n) times inner loop O(n) = O(n^2). Sorting is O(n log n), dominated by O(n^2).
- **Be ready for follow-ups**: 4Sum, kSum generalization, 3Sum Closest.

## Mnemonics / Memory Aids

- **"Fix, Point, Squeeze"**: Fix one element, then use two pointers that squeeze toward each other.
- **"Sort, Skip, Scan"**: Sort the array, skip duplicates, scan with two pointers.
- **"Three levels of dedup"**: Skip duplicates at `i`, skip duplicates at `left`, skip duplicates at `right`.
- **"Negative goes left, positive goes right"**: If sum is too small, move left pointer right (increase sum); if too large, move right pointer left (decrease sum).

## Self-Assessment Checklist

- [ ] Can I explain why sorting enables the two-pointer approach?
- [ ] Can I implement duplicate skipping at all three positions correctly?
- [ ] Can I trace through `[-1, 0, 1, 2, -1, -4]` and produce the correct output?
- [ ] Do I understand the early termination when `nums[i] > 0`?
- [ ] Can I generalize this to kSum?
- [ ] Can I explain why time complexity is O(n^2) and not O(n^2 log n)?

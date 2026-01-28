# Container With Most Water - Study Guide

## Problem Summary
- **Difficulty**: Medium / **Category**: Two Pointers / **Tags**: Array, Two Pointers, Greedy / **LeetCode Link**: https://leetcode.com/problems/container-with-most-water/

> **One-liner**: Find two lines in an array of heights that form a container holding the maximum amount of water.

## Key Intuition

The area between two lines is `min(height[left], height[right]) * (right - left)`. Starting with the widest container (pointers at both ends), you can only improve the area by moving the pointer at the shorter line inward, since moving the taller line's pointer guarantees a smaller width with no chance of a higher limiting height. This greedy elimination ensures no optimal pair is skipped.

## Approaches Overview

| Approach | Time | Space | Trade-off |
|----------|------|-------|-----------|
| Brute Force (all pairs) | O(n^2) | O(1) | Simple but too slow for large inputs |
| Two Pointers (greedy) | O(n) | O(1) | Optimal -- single pass with constant space |

## Approach 1: Brute Force

### Core Idea
Try every pair of lines `(i, j)` and compute the area. Track the maximum.

### Code

```python
class Solution:
    def maxArea(self, height: list[int]) -> int:
        max_area = 0
        n = len(height)
        for i in range(n):
            for j in range(i + 1, n):
                area = min(height[i], height[j]) * (j - i)
                max_area = max(max_area, area)
        return max_area
```

### Walkthrough
Using `height = [1,8,6,2,5,4,8,3,7]`:
- Pair (1, 8) at indices (0, 1): area = min(1,8) * 1 = 1
- Pair (1, 7) at indices (0, 8): area = min(1,7) * 8 = 8
- Pair (8, 7) at indices (1, 8): area = min(8,7) * 7 = 49
- ...continues checking all 36 pairs, maximum found is 49.

## Approach 2: Two Pointers (Optimal)

### Core Idea
Place pointers at both ends. Compute the area, then move the pointer pointing to the shorter line inward. The reasoning: the shorter line is the bottleneck. Keeping it and shrinking the width can never help, so the only hope for a larger area is to find a taller replacement.

### Code

```python
class Solution:
    def maxArea(self, height: list[int]) -> int:
        left = 0
        right = len(height) - 1
        max_area = 0

        while left < right:
            width = right - left
            h = min(height[left], height[right])
            max_area = max(max_area, width * h)

            if height[left] < height[right]:
                left += 1
            else:
                right -= 1

        return max_area
```

### Walkthrough
Using `height = [1,8,6,2,5,4,8,3,7]`:
- `left=0, right=8`: h=min(1,7)=1, width=8, area=8. height[0]<height[8], move left.
- `left=1, right=8`: h=min(8,7)=7, width=7, area=49. height[1]>height[8], move right.
- `left=1, right=7`: h=min(8,3)=3, width=6, area=18. height[7]<height[1], move right.
- `left=1, right=6`: h=min(8,8)=8, width=5, area=40. Equal, move right.
- `left=1, right=5`: h=min(8,4)=4, width=4, area=16. Move right.
- `left=1, right=4`: h=min(8,5)=5, width=3, area=15. Move right.
- `left=1, right=3`: h=min(8,2)=2, width=2, area=4. Move right.
- `left=1, right=2`: h=min(8,6)=6, width=1, area=6. Move right.
- `left < right` is false, stop. Return `max_area = 49`.

## Complexity Analysis

- **Time**: O(n) -- Each pointer moves at most n-1 times; each step performs O(1) work.
- **Space**: O(1) -- Only three integer variables are used regardless of input size.

## Common Mistakes

1. **Moving the taller pointer instead of the shorter one**: This breaks the greedy invariant. Moving the taller line can only decrease or maintain the limiting height while shrinking the width, so it can never lead to a better solution.
2. **Using height difference instead of min**: The container's water level is determined by the shorter line (`min`), not the difference between heights.
3. **Off-by-one in width calculation**: The width is `right - left`, not `right - left + 1`. The lines are at positions `left` and `right`, and the distance between them is their index difference.
4. **Forgetting to handle equal heights**: When both heights are equal, it does not matter which pointer you move -- both are valid. A common bug is adding special-case logic that complicates the solution unnecessarily.

## Interview Tips

- **How to communicate**: Start by explaining the brute force O(n^2) approach, then introduce the two-pointer optimization. Explain *why* moving the shorter pointer is safe -- this is the most critical reasoning step.
- **Clarifying questions to ask**: "Can heights be zero?" (Yes). "Is the array guaranteed to have at least 2 elements?" (Yes, per constraints). "Are heights always non-negative?" (Yes).
- **Optimization path**: Brute force O(n^2) -> Two pointers O(n). Mention that the greedy proof relies on the fact that all "skipped" pairs are provably suboptimal.
- **Follow-up variations**: "What if you can use 3 lines?" leads to a different problem. "What if lines have width?" changes the area formula. Trapping Rain Water (#42) is the natural hard follow-up.

## Mnemonics / Memory Aids

- **"Shrink the short side"**: Always move the pointer at the shorter line. The short side is the bottleneck.
- **"Wide start, narrow finish"**: Start with the widest container and systematically narrow it, guaranteeing you check the best candidates.
- **"Area = min * width"**: The formula is always `min(left_height, right_height) * (right - left)`.

## Self-Assessment Checklist
- [ ] Can I identify this problem type within 2 minutes?
- [ ] Can I code the optimal solution without looking at notes?
- [ ] Can I explain the time/space complexity clearly?
- [ ] Can I handle all edge cases?
- [ ] Can I discuss trade-offs between approaches?

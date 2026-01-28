# Largest Rectangle in Histogram - Study Guide

## Problem Summary
- **Difficulty**: Hard / **Category**: Stack / **Tags**: Array, Stack, Monotonic Stack / **LeetCode Link**: https://leetcode.com/problems/largest-rectangle-in-histogram/

> **One-liner**: Find the area of the largest rectangle that can be formed within a histogram of bar heights.

## Key Intuition

For each bar, the largest rectangle using that bar as the height extends as far left and right as possible while all bars remain at least as tall. A monotonic increasing stack efficiently finds these boundaries: when a bar causes a pop, the popped bar has found its right boundary (current index) and left boundary (new stack top). The sentinel value of 0 at the end flushes all remaining bars from the stack.

## Approaches Overview

| Approach | Time | Space | Trade-off |
|----------|------|-------|-----------|
| Brute Force (expand from each bar) | O(n^2) | O(1) | Simple but too slow for n=10^5 |
| Monotonic Stack | O(n) | O(n) | Optimal -- each bar pushed and popped once |

## Approach 1: Brute Force

### Core Idea
For each bar at index `i`, expand left and right while neighboring bars are at least as tall. Area = `height[i] * (right - left + 1)`.

### Code

```python
class Solution:
    def largestRectangleArea(self, heights: list[int]) -> int:
        max_area = 0
        n = len(heights)
        for i in range(n):
            left = i
            while left > 0 and heights[left - 1] >= heights[i]:
                left -= 1
            right = i
            while right < n - 1 and heights[right + 1] >= heights[i]:
                right += 1
            max_area = max(max_area, heights[i] * (right - left + 1))
        return max_area
```

### Walkthrough
`heights = [2,1,5,6,2,3]`:
- i=0, h=2: left=0, right=0. Area = 2*1 = 2.
- i=1, h=1: left=0, right=5. Area = 1*6 = 6.
- i=2, h=5: left=2, right=3. Area = 5*2 = 10.
- i=3, h=6: left=3, right=3. Area = 6*1 = 6.
- i=4, h=2: left=2, right=5. Area = 2*4 = 8.
- i=5, h=3: left=5, right=5. Area = 3*1 = 3.
- Maximum: 10.

## Approach 2: Monotonic Stack (Optimal)

### Core Idea
Use a stack storing indices in increasing order of height. When a bar is shorter than the stack top, pop and compute the area of the popped bar's rectangle. The popped bar's right boundary is the current index, and its left boundary is the new stack top. A sentinel height of 0 at index `n` ensures all bars are eventually processed.

### Code

```python
class Solution:
    def largestRectangleArea(self, heights: list[int]) -> int:
        stack = []
        max_area = 0
        n = len(heights)

        for i in range(n + 1):
            current_height = heights[i] if i < n else 0

            while stack and current_height < heights[stack[-1]]:
                h = heights[stack.pop()]
                w = i if not stack else i - stack[-1] - 1
                max_area = max(max_area, h * w)

            stack.append(i)

        return max_area
```

### Walkthrough
`heights = [2,1,5,6,2,3]`, sentinel at index 6 with height 0:

- i=0: stack empty, push 0. Stack: [0]
- i=1: h[1]=1 < h[0]=2. Pop 0: h=2, w=1 (stack empty, w=i=1). Area=2. Push 1. Stack: [1]
- i=2: h[2]=5 >= h[1]=1. Push 2. Stack: [1,2]
- i=3: h[3]=6 >= h[2]=5. Push 3. Stack: [1,2,3]
- i=4: h[4]=2 < h[3]=6. Pop 3: h=6, w=4-2-1=1. Area=6.
  - h[4]=2 < h[2]=5. Pop 2: h=5, w=4-1-1=2. Area=10.
  - h[4]=2 >= h[1]=1. Push 4. Stack: [1,4]
- i=5: h[5]=3 >= h[4]=2. Push 5. Stack: [1,4,5]
- i=6 (sentinel=0): Pop 5: h=3, w=6-4-1=1. Area=3.
  - Pop 4: h=2, w=6-1-1=4. Area=8.
  - Pop 1: h=1, w=6 (stack empty). Area=6.
- Max area: **10**.

## Complexity Analysis

- **Time**: O(n) -- Each of the n indices is pushed and popped at most once from the stack. The sentinel adds one extra iteration. Total operations: at most 2n pushes/pops.
- **Space**: O(n) -- The stack can hold up to n indices in the worst case (ascending heights).

## Common Mistakes

1. **Forgetting the sentinel**: Without processing a final "height 0" bar, bars remaining in the stack after the loop never have their areas computed. Always iterate to `n + 1` or add a cleanup loop.
2. **Width calculation error**: When the stack is empty after a pop, the width is `i` (the bar extends from index 0 to i-1). When the stack is not empty, the width is `i - stack[-1] - 1`. Mixing these up is the most common bug.
3. **Using height instead of index in the stack**: The stack stores *indices*, not heights. Heights are looked up via `heights[stack[-1]]`.
4. **Confusing < vs <=**: The while condition should be `current_height < heights[stack[-1]]` (strictly less). Equal heights can be part of the same rectangle, so they should be pushed, not trigger pops. (Note: using `<=` also works but computes the same bar's rectangle in a different way; `<` is more standard.)

## Interview Tips

- **How to communicate**: Start by explaining the per-bar intuition: "For each bar, I want to find how far left and right it can extend." Then explain why the monotonic stack finds these boundaries efficiently. Walk through the stack states for a small example.
- **Clarifying questions to ask**: "Can bar heights be zero?" (Yes). "Is the histogram always non-empty?" (Yes, length >= 1).
- **Optimization path**: Brute force O(n^2) -> Monotonic stack O(n). Mention that this is a well-known pattern used as a building block in other problems (e.g., Maximal Rectangle #85).
- **Follow-up variations**: "Find the maximal rectangle in a binary matrix" (LeetCode #85) -- apply this algorithm row by row, building a histogram from the matrix.

## Mnemonics / Memory Aids

- **"Stack stores indices, not heights"**: Always look up heights via `heights[stack[-1]]`.
- **"Pop means right boundary found"**: When a bar pops, the current index `i` is its right boundary.
- **"Empty stack means extends to start"**: If the stack is empty after popping, the bar's rectangle starts at index 0, so width = `i`.
- **"Sentinel flushes the stack"**: Adding height 0 at the end guarantees every bar gets processed.
- **Width formula**: `w = i` if stack empty, else `w = i - stack[-1] - 1`.

## Self-Assessment Checklist
- [ ] Can I identify this problem type within 2 minutes?
- [ ] Can I code the optimal solution without looking at notes?
- [ ] Can I explain the time/space complexity clearly?
- [ ] Can I handle all edge cases?
- [ ] Can I discuss trade-offs between approaches?

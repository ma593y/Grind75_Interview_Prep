# Permutations - Study Guide

## Problem Summary
> **One-liner**: Return all possible orderings of an array of distinct integers.

## Key Intuition

Unlike combinations/subsets where order does not matter, permutations care about the **ordering** of elements. At each position in the permutation, you can place any element that has not been used yet. This naturally forms a decision tree where each level represents filling the next position.

## Approaches Overview

| Approach | Time | Space | Trade-off |
|----------|------|-------|-----------|
| Backtracking with Used Array | O(N! * N) | O(N) | Clear and intuitive, easy to extend |
| Swap-based Backtracking | O(N! * N) | O(N) | In-place, slightly harder to understand |

## Approach 1: Backtracking with Used Array

### Core Idea
Maintain a boolean array tracking which elements are currently in the path. At each step, try every unused element, recurse, then undo the choice.

### Code

```python
class Solution:
    def permute(self, nums: list[int]) -> list[list[int]]:
        result = []
        used = [False] * len(nums)

        def backtrack(path: list[int]) -> None:
            if len(path) == len(nums):
                result.append(path[:])
                return

            for i in range(len(nums)):
                if used[i]:
                    continue

                used[i] = True
                path.append(nums[i])
                backtrack(path)
                path.pop()
                used[i] = False

        backtrack([])
        return result
```

### Walkthrough
Using `nums = [1,2,3]`:
```
backtrack([])
  i=0: path=[1], used=[T,F,F]
    i=1: path=[1,2], used=[T,T,F]
      i=2: path=[1,2,3] -> RECORD [1,2,3]
    i=2: path=[1,3], used=[T,F,T]
      i=1: path=[1,3,2] -> RECORD [1,3,2]
  i=1: path=[2], used=[F,T,F]
    i=0: path=[2,1], used=[T,T,F]
      i=2: path=[2,1,3] -> RECORD [2,1,3]
    i=2: path=[2,3], used=[F,T,T]
      i=0: path=[2,3,1] -> RECORD [2,3,1]
  i=2: path=[3], used=[F,F,T]
    i=0: path=[3,1], used=[T,F,T]
      i=1: path=[3,1,2] -> RECORD [3,1,2]
    i=1: path=[3,2], used=[F,T,T]
      i=0: path=[3,2,1] -> RECORD [3,2,1]
```
Result: [[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]

## Approach 2: Swap-based Backtracking (Optimal)

### Core Idea
Instead of building a separate path and tracking used elements, fix one element at position `start` by swapping it with each subsequent element, then recurse on the remaining positions. This is in-place and avoids the used array.

### Code

```python
class Solution:
    def permute(self, nums: list[int]) -> list[list[int]]:
        result = []

        def backtrack(start: int) -> None:
            if start == len(nums):
                result.append(nums[:])
                return

            for i in range(start, len(nums)):
                nums[start], nums[i] = nums[i], nums[start]
                backtrack(start + 1)
                nums[start], nums[i] = nums[i], nums[start]

        backtrack(0)
        return result
```

### Walkthrough
Using `nums = [1,2,3]`:
```
backtrack(0): try each element at position 0
  swap(0,0): [1,2,3] -> backtrack(1)
    swap(1,1): [1,2,3] -> backtrack(2)
      swap(2,2): [1,2,3] -> RECORD, backtrack(3) base case
    swap(1,2): [1,3,2] -> backtrack(2)
      RECORD [1,3,2]
    swap back: [1,2,3]
  swap(0,1): [2,1,3] -> backtrack(1)
    ...generates [2,1,3] and [2,3,1]
  swap back: [1,2,3]
  swap(0,2): [3,2,1] -> backtrack(1)
    ...generates [3,2,1] and [3,1,2]
  swap back: [1,2,3]
```

## Complexity Analysis

| Aspect | Both Approaches |
|--------|----------------|
| Time | O(N! * N) -- N! permutations, each copied in O(N) |
| Space | O(N) for recursion stack (excluding output) |
| Output Size | N! * N total elements stored |

## Common Mistakes

1. **Forgetting to copy the path**: `result.append(path)` stores a reference that gets modified. Always use `path[:]` or `list(path)`.
2. **Not restoring state after recursion**: Forgetting `path.pop()` or `used[i] = False` breaks the backtracking.
3. **Confusing with combinations**: In permutations, you iterate from 0 each time (not from `start`), and every element must be used exactly once.
4. **Swap-based: not swapping back**: The second swap undoes the first. Without it, the array gets corrupted for subsequent iterations.

## Interview Tips

- **Clarify uniqueness**: Ask if elements are distinct. If not, you need Permutations II (LC #47) with sorting + skip logic.
- **Know both approaches**: The used-array approach is easier to explain; the swap approach shows deeper understanding.
- **State the time complexity upfront**: "There are N! permutations, so any correct solution is at least O(N!)."
- **Compare with subsets/combinations**: Show you understand the differences in the backtracking template.
- **Mention N! growth**: For N=6, there are 720 permutations. For N=10, there are 3.6 million. This is why constraints are small.

## Mnemonics / Memory Aids

- **"Used array = choose, explore, unchoose"**: The three-step pattern of backtracking.
- **"Permutations: loop from 0, Combinations: loop from start"**: Key difference in the for-loop range.
- **"Swap and advance"**: For the swap-based approach, swap element into position, advance start, then swap back.

## Self-Assessment Checklist

- [ ] Can I write the used-array backtracking approach from memory?
- [ ] Can I write the swap-based approach from memory?
- [ ] Do I understand why we loop from 0 (not from start) in permutations?
- [ ] Can I trace the recursion tree for [1,2,3]?
- [ ] Can I explain the time complexity O(N! * N)?
- [ ] Do I know how to handle duplicates (Permutations II)?
- [ ] Can I articulate the difference between permutation and combination backtracking?

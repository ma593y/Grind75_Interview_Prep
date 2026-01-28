# Subsets - Study Guide

## Problem Summary
> **One-liner**: Return all possible subsets (the power set) of an array of unique integers.

## Key Intuition

Every element has a binary choice: either it is in the subset or it is not. This gives 2^N total subsets. You can think of building subsets as a tree where at each level you decide whether to include the next element, or equivalently, you pick which element to add next (starting from the current index to avoid duplicates).

## Approaches Overview

| Approach | Time | Space | Trade-off |
|----------|------|-------|-----------|
| Cascading (Iterative) | O(N * 2^N) | O(N * 2^N) | Simple, no recursion |
| Backtracking | O(N * 2^N) | O(N) stack | Generalizable template |
| Bit Manipulation | O(N * 2^N) | O(N * 2^N) | Elegant, uses bitmask |

## Approach 1: Cascading (Iterative)

### Core Idea
Start with `[[]]`. For each new number, duplicate all existing subsets and append the number to each duplicate.

### Code

```python
class Solution:
    def subsets(self, nums: list[int]) -> list[list[int]]:
        result = [[]]

        for num in nums:
            result += [subset + [num] for subset in result]

        return result
```

### Walkthrough
Using `nums = [1,2,3]`:
```
Start: [[]]
Add 1: [[], [1]]
Add 2: [[], [1], [2], [1,2]]
Add 3: [[], [1], [2], [1,2], [3], [1,3], [2,3], [1,2,3]]
```
Each step doubles the number of subsets.

## Approach 2: Backtracking (Optimal)

### Core Idea
At each recursive call, the current path is already a valid subset -- add it to results immediately. Then try extending it by adding elements from the current index onward.

### Code

```python
class Solution:
    def subsets(self, nums: list[int]) -> list[list[int]]:
        result = []

        def backtrack(start: int, path: list[int]) -> None:
            result.append(path[:])

            for i in range(start, len(nums)):
                path.append(nums[i])
                backtrack(i + 1, path)
                path.pop()

        backtrack(0, [])
        return result
```

### Walkthrough
Using `nums = [1,2,3]`:
```
backtrack(0, [])       -> record []
  i=0: backtrack(1, [1])    -> record [1]
    i=1: backtrack(2, [1,2])  -> record [1,2]
      i=2: backtrack(3, [1,2,3]) -> record [1,2,3]
    i=2: backtrack(3, [1,3])  -> record [1,3]
  i=1: backtrack(2, [2])    -> record [2]
    i=2: backtrack(3, [2,3])  -> record [2,3]
  i=2: backtrack(3, [3])    -> record [3]
```
Result: [[], [1], [1,2], [1,2,3], [1,3], [2], [2,3], [3]]

## Complexity Analysis

| Aspect | All Approaches |
|--------|---------------|
| Time | O(N * 2^N) -- generating 2^N subsets, each up to size N |
| Space | O(N) recursion stack for backtracking; O(N * 2^N) for output |
| Total subsets | Exactly 2^N |

## Common Mistakes

1. **Forgetting the empty subset**: The empty set is always a valid subset. Make sure it is included.
2. **Not copying the path**: `result.append(path)` stores a reference. Use `path[:]` to copy.
3. **Starting from 0 instead of `start`**: This would generate duplicates like [1,2] and [2,1].
4. **Using `i` instead of `i+1` in recursion**: This would allow reusing the same element (turns it into a combination sum problem).

## Interview Tips

- **Know all three approaches**: Cascading, backtracking, and bit manipulation. Each shows different skills.
- **Subsets vs Permutations vs Combinations**: Be clear on differences:
  - Subsets: all possible subsets, no ordering
  - Permutations: all orderings of all elements
  - Combinations: subsets of exactly size k
- **Mention 2^N immediately**: Show you understand the output size.
- **Backtracking template**: This is the simplest backtracking problem -- master it first, then adapt for Combination Sum, Permutations, etc.

## Mnemonics / Memory Aids

- **"Include or Exclude"**: Each element has two choices -- in or out. 2 choices for N elements = 2^N subsets.
- **"Record first, then explore"**: Unlike combination sum (record only when target is met), subsets records every path.
- **"Cascading doubles"**: Each new element doubles the number of subsets.

## Self-Assessment Checklist

- [ ] Can I write the backtracking approach from scratch?
- [ ] Can I write the cascading (iterative) approach?
- [ ] Can I implement the bit manipulation approach?
- [ ] Do I understand why there are exactly 2^N subsets?
- [ ] Can I adapt this template for Subsets II (with duplicates)?
- [ ] Can I trace the recursion tree for [1,2,3]?
- [ ] Do I know the key difference between subsets and permutations backtracking?

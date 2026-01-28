# Combination Sum - Study Guide

## Problem Summary
> **One-liner**: Given distinct integers and a target, find all unique combinations (with reuse allowed) that sum to the target.

## Key Intuition

Think of this as exploring a tree of choices. At each node, you decide: "Which candidate do I add next?" You can reuse the same candidate, but to avoid duplicates like [2,3] and [3,2], you only look at candidates from your current index onward. When the running sum hits the target, you have found a valid combination. When it exceeds the target, you prune that branch.

## Approaches Overview

| Approach | Time | Space | Trade-off |
|----------|------|-------|-----------|
| Brute Force (generate all) | O(N^(T/M)) | O(T/M) | Simple but generates many invalid combinations |
| Backtracking with Pruning | O(N^(T/M)) | O(T/M) | Efficient pruning skips invalid branches early |

## Approach 1: Brute Force

### Core Idea
Generate all possible combinations with replacement up to the maximum possible length, then filter those whose sum equals the target.

### Code

```python
from itertools import combinations_with_replacement

class Solution:
    def combinationSum(self, candidates: list[int], target: int) -> list[list[int]]:
        result = []
        candidates.sort()
        max_len = target // min(candidates)

        for length in range(1, max_len + 1):
            for combo in combinations_with_replacement(candidates, length):
                if sum(combo) == target:
                    result.append(list(combo))

        return result
```

### Walkthrough
Using `candidates = [2,3,6,7], target = 7`:
- max_len = 7 // 2 = 3
- Length 1: (2), (3), (6), (7) -> only (7) sums to 7
- Length 2: (2,2), (2,3), (2,6), (2,7), (3,3), (3,6), (3,7), (6,6), (6,7), (7,7) -> none sum to 7
- Length 3: (2,2,2), (2,2,3), ... -> (2,2,3) sums to 7
- Result: [[7], [2,2,3]]

## Approach 2: Backtracking with Pruning (Optimal)

### Core Idea
Recursively build combinations by choosing candidates from index `start` onward. Subtract the chosen value from the remaining target. If remaining hits 0, record the combination. If remaining goes negative or the current candidate exceeds it (after sorting), prune.

### Code

```python
class Solution:
    def combinationSum(self, candidates: list[int], target: int) -> list[list[int]]:
        result = []
        candidates.sort()

        def backtrack(start: int, remaining: int, path: list[int]) -> None:
            if remaining == 0:
                result.append(path[:])
                return

            for i in range(start, len(candidates)):
                if candidates[i] > remaining:
                    break

                path.append(candidates[i])
                backtrack(i, remaining - candidates[i], path)
                path.pop()

        backtrack(0, target, [])
        return result
```

### Walkthrough
Using `candidates = [2,3,6,7], target = 7`:
```
backtrack(0, 7, [])
  i=0: path=[2], backtrack(0, 5, [2])
    i=0: path=[2,2], backtrack(0, 3, [2,2])
      i=0: path=[2,2,2], backtrack(0, 1, [2,2,2])
        i=0: 2 > 1, break
      i=1: path=[2,2,3], backtrack(1, 0, [2,2,3])
        remaining=0 -> result=[[2,2,3]]
      i=2: 6 > 3, break
    i=1: path=[2,3], backtrack(1, 2, [2,3])
      i=1: 3 > 2, break
    i=2: path=[2,6], 6 > 5? no -> backtrack(2, -1)? Wait, 6 > 5, break
  i=1: path=[3], backtrack(1, 4, [3])
    i=1: path=[3,3], backtrack(1, 1, [3,3])
      i=1: 3 > 1, break
    i=2: 6 > 4, break
  i=2: path=[6], backtrack(2, 1, [6])
    i=2: 6 > 1, break
  i=3: path=[7], backtrack(3, 0, [7])
    remaining=0 -> result=[[2,2,3],[7]]
```

## Complexity Analysis

| Aspect | Brute Force | Backtracking |
|--------|-------------|-------------|
| Time | O(N^(T/M)) | O(N^(T/M)) worst case |
| Space | O(T/M) | O(T/M) recursion depth |
| Practical | Much slower (no pruning) | Much faster (prunes early) |

Where N = number of candidates, T = target, M = minimum candidate value.

## Common Mistakes

1. **Using `i+1` instead of `i` in recursion**: This would prevent reuse of the same element. Use `i` to allow reuse.
2. **Forgetting to copy the path**: `result.append(path)` appends a reference. Use `result.append(path[:])` or `result.append(list(path))`.
3. **Not sorting before pruning**: The `break` optimization only works if candidates are sorted.
4. **Generating duplicates**: If you start from index 0 every time instead of `start`, you get permutations like [2,3] and [3,2].

## Interview Tips

- **Start by clarifying**: Can elements be reused? Are candidates distinct? Can target be 0?
- **Explain the pruning**: Sorting + breaking when candidate > remaining is a key optimization. Mention it explicitly.
- **Draw the recursion tree**: Interviewers love seeing you visualize the backtracking process.
- **Mention the "start index" trick**: This is the standard way to avoid duplicates in combination problems.
- **Know the variants**: Be ready to discuss Combination Sum II (no reuse, duplicates in input) and III (fixed count).

## Mnemonics / Memory Aids

- **"SPA"**: **S**ort, **P**rune, **A**ppend-copy -- the three key steps in backtracking combination problems.
- **"i stays, i+1 goes"**: Use `i` (same index) when reuse is allowed, `i+1` when each element can only be used once.
- **Recursion tree**: Think of each level as "which candidate do I pick next?" and each branch as a different candidate choice.

## Self-Assessment Checklist

- [ ] Can I write the backtracking template from scratch?
- [ ] Do I understand why we pass `i` (not `i+1`) to allow reuse?
- [ ] Can I explain why starting from `start` prevents duplicates?
- [ ] Can I trace through the recursion tree for a small example?
- [ ] Do I know the difference between this and Combination Sum II?
- [ ] Can I identify the time complexity and explain why?
- [ ] Can I implement the pruning optimization (sort + break)?

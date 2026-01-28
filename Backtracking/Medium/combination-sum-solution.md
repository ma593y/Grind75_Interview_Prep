# Combination Sum

## Problem Information
- **Difficulty**: Medium
- **Category**: Backtracking
- **Tags**: Array, Backtracking, Recursion, DFS
- **LeetCode Link**: https://leetcode.com/problems/combination-sum/

## Problem Statement

Given an array of **distinct** integers `candidates` and a target integer `target`, return a list of all **unique combinations** of `candidates` where the chosen numbers sum to `target`. You may return the combinations in **any order**.

The **same** number may be chosen from `candidates` an **unlimited number of times**. Two combinations are unique if the frequency of at least one of the chosen numbers is different.

The test cases are generated such that the number of unique combinations that sum up to `target` is less than 150 combinations for the given input.

### Examples

**Example 1:**
```
Input: candidates = [2,3,6,7], target = 7
Output: [[2,2,3],[7]]
Explanation:
2 and 3 are candidates, and 2 + 2 + 3 = 7. Note that 2 can be used multiple times.
7 is a candidate, and 7 = 7.
These are the only two combinations.
```

**Example 2:**
```
Input: candidates = [2,3,5], target = 8
Output: [[2,2,2,2],[2,3,3],[3,5]]
```

**Example 3:**
```
Input: candidates = [2], target = 1
Output: []
```

### Constraints
- `1 <= candidates.length <= 30`
- `2 <= candidates[i] <= 40`
- All elements of `candidates` are **distinct**.
- `1 <= target <= 40`

## Core Concept / Pattern

This is a classic **backtracking** problem where we explore all possible combinations by making choices and undoing them (backtracking) when they don't lead to a valid solution. The key insight is that we can reuse elements, and we avoid duplicates by only considering candidates from the current index onward.

## Approach 1: Brute Force (Generate All and Filter)

### Intuition
Generate every possible combination of candidates (with repetition allowed) up to a maximum length, then filter for those that sum to the target. This is extremely wasteful because it explores many combinations that clearly exceed the target.

### Algorithm
1. Generate all combinations of all lengths from 1 to target/min(candidates).
2. For each combination, check if the sum equals the target.
3. Collect valid combinations.

### Implementation

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

### Complexity Analysis
- **Time Complexity**: O(N^T) where N is the number of candidates and T is related to target/min(candidate). Exponential due to generating all combinations.
- **Space Complexity**: O(T/min(candidate)) for storing combinations.

### Why We Can Do Better
The brute force generates combinations that clearly exceed the target. By using backtracking with pruning, we can abandon paths early when the running sum exceeds the target.

## Approach 2: Optimal Solution (Backtracking with Pruning)

### Intuition
Use a recursive backtracking approach. At each step, we try adding each candidate (from the current index onward) to our current combination. If the remaining target becomes 0, we found a valid combination. If it goes negative, we prune that branch. By only considering candidates from the current index onward, we avoid generating duplicate combinations.

### Algorithm
1. Sort candidates (optional but helps with pruning).
2. Define a recursive function `backtrack(start, remaining, path)`:
   - If `remaining == 0`, add a copy of `path` to results.
   - If `remaining < 0`, return (prune).
   - For each candidate from index `start` to end:
     - If candidate > remaining, break (pruning with sorted array).
     - Add candidate to path.
     - Recurse with same start index (allowing reuse).
     - Remove candidate from path (backtrack).
3. Return all collected results.

### Implementation

```python
class Solution:
    def combinationSum(self, candidates: list[int], target: int) -> list[list[int]]:
        result = []
        candidates.sort()

        def backtrack(start: int, remaining: int, path: list[int]) -> None:
            if remaining == 0:
                result.append(path[:])  # append a copy
                return

            for i in range(start, len(candidates)):
                if candidates[i] > remaining:
                    break  # pruning: sorted so all subsequent are larger

                path.append(candidates[i])
                backtrack(i, remaining - candidates[i], path)  # i, not i+1, allows reuse
                path.pop()  # backtrack

        backtrack(0, target, [])
        return result
```

### Complexity Analysis
- **Time Complexity**: O(N^(T/M)) where N is the number of candidates, T is the target value, and M is the minimum candidate value. In the worst case, the recursion tree has depth T/M and branching factor N.
- **Space Complexity**: O(T/M) for the recursion stack depth (the deepest path uses the smallest candidate repeatedly).

## Edge Cases

| Edge Case | Expected Behavior | Why It Matters |
|-----------|-------------------|----------------|
| Single candidate equals target | Return [[candidate]] | Base case with one valid combination |
| Single candidate doesn't divide target | Return [] | No combination possible |
| Target smaller than all candidates | Return [] | No candidate can be used |
| All candidates equal to target | Each forms its own combination | Multiple single-element solutions |
| Very large target with small candidates | Many combinations | Tests recursion depth and pruning efficiency |

## Related Problems

| Problem | Relationship | Difficulty |
|---------|-------------|------------|
| Combination Sum II (LC #40) | No reuse allowed, candidates may have duplicates | Medium |
| Combination Sum III (LC #216) | Fixed count of numbers, digits 1-9 only | Medium |
| Subsets (LC #78) | Similar backtracking structure without sum constraint | Medium |
| Permutations (LC #46) | Backtracking with ordering matters | Medium |

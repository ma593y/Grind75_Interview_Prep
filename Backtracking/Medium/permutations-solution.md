# Permutations

## Problem Information
- **Difficulty**: Medium
- **Category**: Backtracking
- **Tags**: Array, Backtracking, Recursion
- **LeetCode Link**: https://leetcode.com/problems/permutations/

## Problem Statement

Given an array `nums` of distinct integers, return all the possible permutations. You can return the answer in **any order**.

### Examples

**Example 1:**
```
Input: nums = [1,2,3]
Output: [[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]
```

**Example 2:**
```
Input: nums = [0,1]
Output: [[0,1],[1,0]]
```

**Example 3:**
```
Input: nums = [1]
Output: [[1]]
```

### Constraints
- `1 <= nums.length <= 6`
- `-10 <= nums[i] <= 10`
- All the integers of `nums` are **unique**.

## Core Concept / Pattern

Permutations require considering **every possible ordering** of the elements. Unlike combinations/subsets where order does not matter, here [1,2] and [2,1] are different results. The backtracking approach builds permutations one element at a time, tracking which elements have already been used.

## Approach 1: Brute Force (Using Built-in)

### Intuition
Python's `itertools.permutations` generates all permutations directly. While not useful in an interview, it validates our understanding of the expected output.

### Algorithm
1. Use `itertools.permutations` to generate all orderings.
2. Convert each tuple to a list.

### Implementation

```python
from itertools import permutations

class Solution:
    def permute(self, nums: list[int]) -> list[list[int]]:
        return [list(p) for p in permutations(nums)]
```

### Complexity Analysis
- **Time Complexity**: O(N! * N) -- N! permutations, each of length N.
- **Space Complexity**: O(N! * N) to store all permutations.

### Why We Can Do Better
This doesn't demonstrate understanding of the algorithm. In an interview, you need to implement the backtracking logic yourself.

## Approach 2: Optimal Solution (Backtracking with Used Set)

### Intuition
Build each permutation element by element. At each position, try every number that hasn't been used yet. When the permutation reaches full length, record it. Then backtrack (remove the last element, mark it unused) and try the next option.

### Algorithm
1. Maintain a `used` boolean array (or set) to track which elements are currently in the path.
2. Define `backtrack(path)`:
   - If `len(path) == len(nums)`, append a copy of path to results.
   - For each index `i` in nums:
     - If `used[i]` is True, skip.
     - Mark `used[i] = True`, append `nums[i]` to path.
     - Recurse.
     - Remove `nums[i]` from path, mark `used[i] = False`.
3. Return results.

### Implementation

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

**Alternative (Swap-based approach):**

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

### Complexity Analysis
- **Time Complexity**: O(N! * N) -- there are N! permutations and copying each takes O(N).
- **Space Complexity**: O(N) for the recursion stack and the used array (excluding output storage).

## Edge Cases

| Edge Case | Expected Behavior | Why It Matters |
|-----------|-------------------|----------------|
| Single element `[1]` | Return `[[1]]` | Only one permutation exists |
| Two elements `[1,2]` | Return `[[1,2],[2,1]]` | Smallest non-trivial case |
| Negative numbers `[-1,0,1]` | All 6 permutations | Negatives don't affect logic |
| Maximum length (6 elements) | 720 permutations | Tests completeness |

## Related Problems

| Problem | Relationship | Difficulty |
|---------|-------------|------------|
| Permutations II (LC #47) | Handles duplicate elements | Medium |
| Subsets (LC #78) | Order doesn't matter, different backtracking structure | Medium |
| Combination Sum (LC #39) | Backtracking with sum constraint | Medium |
| Next Permutation (LC #31) | Find just the next permutation in sequence | Medium |

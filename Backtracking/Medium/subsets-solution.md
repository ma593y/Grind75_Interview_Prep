# Subsets

## Problem Information
- **Difficulty**: Medium
- **Category**: Backtracking
- **Tags**: Array, Backtracking, Bit Manipulation
- **LeetCode Link**: https://leetcode.com/problems/subsets/

## Problem Statement

Given an integer array `nums` of **unique** elements, return all possible subsets (the power set).

The solution set **must not** contain duplicate subsets. Return the solution in **any order**.

### Examples

**Example 1:**
```
Input: nums = [1,2,3]
Output: [[],[1],[2],[1,2],[3],[1,3],[2,3],[1,2,3]]
```

**Example 2:**
```
Input: nums = [0]
Output: [[],[0]]
```

### Constraints
- `1 <= nums.length <= 10`
- `-10 <= nums[i] <= 10`
- All the numbers of `nums` are **unique**.

## Core Concept / Pattern

The power set of N elements has 2^N subsets. Each element is either **included** or **excluded** from a subset. This binary choice naturally maps to both backtracking (include/exclude decision tree) and bit manipulation (each bit represents include/exclude).

## Approach 1: Brute Force (Iterative / Cascading)

### Intuition
Start with an empty set. For each number in the input, take all existing subsets and create new subsets by adding the current number to each of them.

### Algorithm
1. Initialize result with `[[]]` (empty subset).
2. For each number in nums:
   - For each existing subset in result, create a new subset by appending the current number.
   - Add all new subsets to result.
3. Return result.

### Implementation

```python
class Solution:
    def subsets(self, nums: list[int]) -> list[list[int]]:
        result = [[]]

        for num in nums:
            result += [subset + [num] for subset in result]

        return result
```

### Complexity Analysis
- **Time Complexity**: O(N * 2^N) -- for each of the 2^N subsets, we copy it (up to O(N) elements).
- **Space Complexity**: O(N * 2^N) for storing all subsets.

### Why We Can Do Better
This approach is already optimal in terms of time complexity since we must generate 2^N subsets. However, the backtracking approach is more generalizable to related problems (subsets with constraints, combinations, etc.).

## Approach 2: Optimal Solution (Backtracking)

### Intuition
Use a recursive approach where at each step we decide to include or exclude the current element. Alternatively, iterate through remaining elements and choose which one to add next. The key is starting from the current index to avoid duplicates.

### Algorithm
1. Define `backtrack(start, path)`:
   - Add a copy of the current path to results (every path is a valid subset).
   - For each index `i` from `start` to end:
     - Add `nums[i]` to path.
     - Recurse with `i + 1`.
     - Remove `nums[i]` from path (backtrack).
2. Start with `backtrack(0, [])`.

### Implementation

```python
class Solution:
    def subsets(self, nums: list[int]) -> list[list[int]]:
        result = []

        def backtrack(start: int, path: list[int]) -> None:
            result.append(path[:])  # every path is a valid subset

            for i in range(start, len(nums)):
                path.append(nums[i])
                backtrack(i + 1, path)
                path.pop()

        backtrack(0, [])
        return result
```

**Alternative (Bit Manipulation):**

```python
class Solution:
    def subsets(self, nums: list[int]) -> list[list[int]]:
        n = len(nums)
        result = []

        for mask in range(1 << n):  # 0 to 2^n - 1
            subset = []
            for i in range(n):
                if mask & (1 << i):
                    subset.append(nums[i])
            result.append(subset)

        return result
```

### Complexity Analysis
- **Time Complexity**: O(N * 2^N) -- 2^N subsets, each up to length N.
- **Space Complexity**: O(N) for the recursion stack (excluding output).

## Edge Cases

| Edge Case | Expected Behavior | Why It Matters |
|-----------|-------------------|----------------|
| Single element `[1]` | `[[], [1]]` | Smallest non-trivial power set |
| Empty consideration | Always includes `[]` | Empty set is always a subset |
| Negative numbers `[-1, 0]` | `[[], [-1], [0], [-1, 0]]` | Values don't affect algorithm logic |
| Maximum length (10) | 1024 subsets | Tests completeness |

## Related Problems

| Problem | Relationship | Difficulty |
|---------|-------------|------------|
| Subsets II (LC #90) | Input may contain duplicates | Medium |
| Combinations (LC #77) | Subsets of fixed size k | Medium |
| Combination Sum (LC #39) | Subsets with sum constraint and reuse | Medium |
| Permutations (LC #46) | All orderings instead of subsets | Medium |

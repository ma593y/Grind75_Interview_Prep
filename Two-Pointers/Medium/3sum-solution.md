# 3Sum

## Problem Information
- **Difficulty**: Medium
- **Category**: Two Pointers
- **Tags**: Array, Two Pointers, Sorting
- **LeetCode Link**: https://leetcode.com/problems/3sum/

## Problem Statement

Given an integer array `nums`, return all the triplets `[nums[i], nums[j], nums[k]]` such that `i != j`, `i != k`, and `j != k`, and `nums[i] + nums[j] + nums[k] == 0`.

Notice that the solution set must not contain duplicate triplets.

### Examples

**Example 1:**
```
Input: nums = [-1, 0, 1, 2, -1, -4]
Output: [[-1, -1, 2], [-1, 0, 1]]
Explanation:
nums[0] + nums[1] + nums[2] = (-1) + 0 + 1 = 0
nums[1] + nums[2] + nums[4] = 0 + 1 + (-1) = 0
nums[0] + nums[3] + nums[4] = (-1) + 2 + (-1) = 0
The distinct triplets are [-1,0,1] and [-1,-1,2].
```

**Example 2:**
```
Input: nums = [0, 1, 1]
Output: []
Explanation: The only possible triplet does not sum up to 0.
```

**Example 3:**
```
Input: nums = [0, 0, 0]
Output: [[0, 0, 0]]
```

### Constraints
- `3 <= nums.length <= 3000`
- `-10^5 <= nums[i] <= 10^5`

## Core Concept / Pattern

Reduce the 3Sum problem to multiple 2Sum problems. Sort the array, fix one element, and use the two-pointer technique on the remaining sorted subarray to find pairs that sum to the negation of the fixed element. Skip duplicates to avoid repeated triplets.

## Approach 1: Brute Force

### Intuition

Check every combination of three elements. Use a set of sorted tuples to avoid duplicate triplets.

### Algorithm

1. Use three nested loops for indices `i < j < k`.
2. Check if `nums[i] + nums[j] + nums[k] == 0`.
3. Add the sorted triplet to a set to handle duplicates.
4. Convert the set to a list of lists.

### Implementation

```python
class Solution:
    def threeSum(self, nums: list[int]) -> list[list[int]]:
        result = set()
        n = len(nums)
        for i in range(n):
            for j in range(i + 1, n):
                for k in range(j + 1, n):
                    if nums[i] + nums[j] + nums[k] == 0:
                        triplet = tuple(sorted([nums[i], nums[j], nums[k]]))
                        result.add(triplet)
        return [list(t) for t in result]
```

### Complexity Analysis
- **Time Complexity**: O(n^3) for the three nested loops plus O(n log n) per triplet for sorting, but sorting 3 elements is O(1), so overall O(n^3).
- **Space Complexity**: O(n) for the result set (excluding output).

### Why We Can Do Better

Three nested loops are very slow. By sorting the array first, we can use two pointers to eliminate the innermost loop, reducing time complexity to O(n^2).

## Approach 2: Optimal Solution (Sort + Two Pointers)

### Intuition

Sort the array. For each element `nums[i]`, use two pointers (`left` and `right`) on the remaining subarray to find pairs summing to `-nums[i]`. Skip duplicate values of `nums[i]`, `nums[left]`, and `nums[right]` to avoid duplicate triplets.

### Algorithm

1. Sort `nums`.
2. For each index `i` from 0 to n-3:
   - Skip if `nums[i] == nums[i-1]` (avoid duplicate first elements).
   - If `nums[i] > 0`, break (no solution possible since array is sorted).
   - Set `left = i + 1`, `right = n - 1`.
   - While `left < right`:
     - Compute `total = nums[i] + nums[left] + nums[right]`.
     - If `total < 0`, increment `left`.
     - If `total > 0`, decrement `right`.
     - If `total == 0`, record triplet, then skip duplicates for both `left` and `right`.
3. Return all found triplets.

### Implementation

```python
class Solution:
    def threeSum(self, nums: list[int]) -> list[list[int]]:
        nums.sort()
        result = []
        n = len(nums)

        for i in range(n - 2):
            # Skip duplicate values for the first element
            if i > 0 and nums[i] == nums[i - 1]:
                continue

            # Early termination: if smallest element is positive, no triplet sums to 0
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
                    # Skip duplicates for left pointer
                    while left < right and nums[left] == nums[left + 1]:
                        left += 1
                    # Skip duplicates for right pointer
                    while left < right and nums[right] == nums[right - 1]:
                        right -= 1
                    left += 1
                    right -= 1

        return result
```

### Complexity Analysis
- **Time Complexity**: O(n^2). Sorting is O(n log n). The outer loop runs O(n) times, and the inner two-pointer scan is O(n) per iteration, giving O(n^2) total.
- **Space Complexity**: O(1) extra space (excluding the output array). Sorting may use O(log n) stack space depending on implementation.

## Edge Cases

| Edge Case | Input | Expected Output | Why |
|-----------|-------|-----------------|-----|
| All zeros | `[0, 0, 0]` | `[[0, 0, 0]]` | Only triplet that sums to 0 |
| No solution | `[1, 2, 3]` | `[]` | All positive, no triplet sums to 0 |
| All duplicates | `[-1, -1, -1, 2, 2]` | `[[-1, -1, 2]]` | Duplicates must be skipped |
| Two elements | `[1, -1]` | `[]` | Not enough elements (constraint says >= 3) |
| Large negatives | `[-5, -3, -1, 0, 1, 3, 5]` | `[[-5,0,5],[-3,-1,4]...]` | Mix of signs |
| Minimum length | `[0, 0, 0]` | `[[0, 0, 0]]` | Exactly 3 elements |

## Related Problems

| Problem | Difficulty | Why Related |
|---------|------------|-------------|
| [Two Sum (LeetCode #1)](https://leetcode.com/problems/two-sum/) | Easy | Foundation: find two numbers summing to target |
| [3Sum Closest (LeetCode #16)](https://leetcode.com/problems/3sum-closest/) | Medium | Variation: find closest sum instead of exact |
| [4Sum (LeetCode #18)](https://leetcode.com/problems/4sum/) | Medium | Extension to four elements |
| [Two Sum II - Sorted (LeetCode #167)](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/) | Medium | Core two-pointer pattern on sorted array |

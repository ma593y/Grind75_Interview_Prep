# Partition Equal Subset Sum

## Problem Information
- **Difficulty**: Medium
- **Category**: Dynamic Programming
- **Tags**: Array, Dynamic Programming
- **LeetCode Link**: https://leetcode.com/problems/partition-equal-subset-sum/

## Problem Statement
Given an integer array `nums`, return `true` if you can partition the array into two subsets such that the sum of the elements in both subsets is equal.

### Examples
**Example 1:**
```
Input: nums = [1, 5, 11, 5]
Output: true
Explanation: The array can be partitioned as [1, 5, 5] and [11].
```

**Example 2:**
```
Input: nums = [1, 2, 3, 5]
Output: false
Explanation: The array cannot be partitioned into equal sum subsets.
```

### Constraints
- `1 <= nums.length <= 200`
- `1 <= nums[i] <= 100`

## Core Concept / Pattern
If the total sum is odd, partitioning is impossible. If even, the problem reduces to: "can we find a subset that sums to `total / 2`?" This is the classic 0/1 knapsack subset sum problem.

## Approach 1: Brute Force (Recursive)
### Intuition
For each element, decide whether to include it in the first subset or not. Check if any combination sums to `total / 2`.

### Algorithm
1. Compute total sum. If odd, return false.
2. Set `target = total // 2`.
3. Recursively try including or excluding each element.
4. If any path reaches a sum of exactly `target`, return true.

### Implementation
```python
def canPartition(nums: list[int]) -> bool:
    total = sum(nums)
    if total % 2 != 0:
        return False
    target = total // 2

    def backtrack(index, current_sum):
        if current_sum == target:
            return True
        if index >= len(nums) or current_sum > target:
            return False
        # Include or exclude nums[index]
        return (backtrack(index + 1, current_sum + nums[index]) or
                backtrack(index + 1, current_sum))

    return backtrack(0, 0)
```

### Complexity Analysis
- **Time Complexity**: O(2^n) -- two choices per element.
- **Space Complexity**: O(n) -- recursion depth.

### Why We Can Do Better
The subproblems defined by `(index, current_sum)` repeat heavily. Using DP eliminates redundant computation.

## Approach 2: Optimal Solution (1D DP -- 0/1 Knapsack)
### Intuition
Use a boolean DP set (or array) where `dp[s]` indicates whether sum `s` is achievable using a subset of elements seen so far. Process each number and update the set from right to left to avoid using the same element twice.

### Algorithm
1. Compute total. If odd, return false. Set `target = total // 2`.
2. Create `dp` boolean array of size `target + 1`, set `dp[0] = True`.
3. For each num in nums:
   - Iterate `s` from `target` down to `num`:
     - `dp[s] = dp[s] or dp[s - num]`
4. Return `dp[target]`.

### Implementation
```python
def canPartition(nums: list[int]) -> bool:
    total = sum(nums)
    if total % 2 != 0:
        return False
    target = total // 2

    dp = [False] * (target + 1)
    dp[0] = True

    for num in nums:
        for s in range(target, num - 1, -1):
            dp[s] = dp[s] or dp[s - num]

    return dp[target]
```

### Complexity Analysis
- **Time Complexity**: O(n * target) where target = total / 2.
- **Space Complexity**: O(target) for the 1D dp array.

## Edge Cases
| Edge Case | Expected Behavior | Why It Matters |
|-----------|-------------------|----------------|
| Odd total sum | Return false | Cannot split odd sum equally |
| Single element | Return false | Cannot form two non-empty subsets |
| All elements equal | True if even count | e.g., [5, 5] -> true, [5, 5, 5] -> false |
| Contains a zero | Still works | Zero does not affect sums |
| One element equals half the sum | Return true if remaining elements sum to the other half | Direct partition |

## Related Problems
| Problem | Relationship | Difficulty |
|---------|-------------|------------|
| 0/1 Knapsack | Direct application of subset sum | Medium |
| Target Sum (LC #494) | Assign +/- to each element to reach target | Medium |
| Last Stone Weight II (LC #1049) | Minimize remaining weight using subset sum | Medium |
| Coin Change (LC #322) | Unbounded knapsack variant | Medium |
| Subset Sum | Core subroutine of this problem | Medium |

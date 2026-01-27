# Partition Equal Subset Sum - Study Guide

## Problem Summary
- **Difficulty**: Medium
- **Category**: Dynamic Programming
- **Tags**: Array, Dynamic Programming
- **LeetCode Link**: https://leetcode.com/problems/partition-equal-subset-sum/

> **One-liner**: Determine if an array can be split into two subsets with equal sums (reduces to 0/1 knapsack subset sum for total/2).

## Key Intuition
Two subsets with equal sums means each subset sums to `total / 2`. So the question becomes: "can we pick a subset that sums to exactly `total / 2`?" This is the classic subset sum problem, solvable with 0/1 knapsack DP. If the total is odd, the answer is immediately false.

## Approaches Overview
| Approach | Time | Space | Trade-off |
|----------|------|-------|-----------|
| Brute Force Recursion | O(2^n) | O(n) | Exponential, tries all subsets |
| 1D DP (0/1 Knapsack) | O(n * target) | O(target) | Optimal, pseudo-polynomial |

## Approach 1: Brute Force Recursion
### Core Idea
For each element, decide to include it or not. If any combination sums to `target`, return true.

### Code
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
        return (backtrack(index + 1, current_sum + nums[index]) or
                backtrack(index + 1, current_sum))

    return backtrack(0, 0)
```

### Walkthrough
For `nums = [1, 5, 11, 5]`, target = 11:
- Include 1, include 5, exclude 11, include 5: sum = 1+5+5 = 11. Return True.

## Approach 2: 1D DP (Optimal)
### Core Idea
Maintain a boolean array `dp` where `dp[s]` means "can we form sum `s`?" Process each number right-to-left to ensure each element is used at most once.

### Code
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

### Walkthrough
For `nums = [1, 5, 11, 5]`, target = 11:
```
Initial: dp = [T, F, F, F, F, F, F, F, F, F, F, F]

After num=1:  dp = [T, T, F, F, F, F, F, F, F, F, F, F]
After num=5:  dp = [T, T, F, F, F, T, T, F, F, F, F, F]
After num=11: dp = [T, T, F, F, F, T, T, F, F, F, F, T]  (11=11, 6+5 skipped)
After num=5:  dp = [T, T, F, F, F, T, T, F, F, F, T, T]
```
dp[11] = True. Return True.

## Complexity Analysis
| Approach | Time | Space | Explanation |
|----------|------|-------|-------------|
| Brute Force | O(2^n) | O(n) | All subsets explored |
| 1D DP | O(n * target) | O(target) | target = sum/2, pseudo-polynomial |

## Common Mistakes
1. **Forgetting the odd-sum check**: If `total % 2 != 0`, immediately return false.
2. **Iterating left-to-right instead of right-to-left**: Left-to-right allows the same element to be used multiple times (unbounded knapsack instead of 0/1).
3. **Using a 2D array when 1D suffices**: The 1D optimization is standard for 0/1 knapsack and saves space.
4. **Not recognizing the reduction**: The hardest part is seeing that equal partition = subset sum for total/2.

## Interview Tips
- **How to communicate**: "If the total is even, I need to find a subset summing to total/2. This is the 0/1 knapsack subset sum problem."
- **Clarifying questions to ask**: Can the array be empty? Are all values positive? (Constraints say yes to positive.)
- **Optimization path**: "Brute force is O(2^n). DP brings it down to O(n * sum/2) using the knapsack technique."
- **Follow-up variations**: What if you need to find the actual partition? (Backtrack through the DP table.) What if elements can be negative? (Different approach needed.)

## Mnemonics / Memory Aids
- **"Odd? Odd out."**: If total sum is odd, return false immediately.
- **"Half the sum, full the knapsack"**: Target is total/2, solve with 0/1 knapsack.
- **"Right to left, one use"**: Iterate the inner loop backward to ensure each element is used once.

## Self-Assessment Checklist
- [ ] Can I reduce equal partition to subset sum within 2 minutes?
- [ ] Can I write the 1D knapsack DP without notes?
- [ ] Can I explain why the inner loop goes right to left?
- [ ] Can I handle the odd-sum case immediately?
- [ ] Can I trace through the DP table for a small example?

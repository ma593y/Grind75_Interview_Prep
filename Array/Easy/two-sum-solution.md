# Two Sum

## Problem Information
- **Difficulty**: Easy
- **Category**: Array
- **Tags**: Array, Hash Table
- **LeetCode Link**: https://leetcode.com/problems/two-sum/

## Problem Statement
Given an array of integers `nums` and an integer `target`, return the indices of the two numbers such that they add up to `target`.

You may assume that each input would have exactly one solution, and you may not use the same element twice.

You can return the answer in any order.

### Examples
**Example 1:**
```
Input: nums = [2, 7, 11, 15], target = 9
Output: [0, 1]
Explanation: Because nums[0] + nums[1] == 9, we return [0, 1].
```

**Example 2:**
```
Input: nums = [3, 2, 4], target = 6
Output: [1, 2]
```

**Example 3:**
```
Input: nums = [3, 3], target = 6
Output: [0, 1]
```

### Constraints
- 2 <= nums.length <= 10^4
- -10^9 <= nums[i] <= 10^9
- -10^9 <= target <= 10^9
- Only one valid answer exists.

## Core Concept / Pattern
This problem tests the fundamental concept of using a hash map to achieve constant-time lookups. The key insight is that for each number in the array, we know exactly what complement we need (target - current number), and we can use a hash map to check if we have already seen that complement.

This is one of the most foundational patterns in coding interviews. The transition from brute force O(n^2) to optimal O(n) by trading space for time via a hash map is a pattern that appears in dozens of other problems.

## Approach 1: Brute Force

### Intuition
The simplest approach is to check every pair of numbers in the array and see if they sum to the target. We use two nested loops: the outer loop picks the first number, and the inner loop picks the second number.

### Algorithm
1. Iterate through each element `nums[i]` with index `i`.
2. For each `nums[i]`, iterate through every subsequent element `nums[j]` where `j > i`.
3. Check if `nums[i] + nums[j] == target`.
4. If found, return `[i, j]`.

### Implementation
```python
def twoSum(nums, target):
    for i in range(len(nums)):
        for j in range(i + 1, len(nums)):
            if nums[i] + nums[j] == target:
                return [i, j]
    return []
```

### Complexity Analysis
- **Time Complexity**: O(n^2) -- Two nested loops, each iterating up to n elements.
- **Space Complexity**: O(1) -- No additional data structures used.

### Why We Can Do Better
For every element, we scan the rest of the array to find its complement. This redundant scanning can be eliminated by storing previously seen values in a hash map, allowing O(1) lookup instead of O(n) scanning.

## Approach 2: Optimal Solution (Hash Map)

### Intuition
Instead of searching for the complement in the remaining array, we can store each number and its index in a hash map as we iterate. For each new number, we check if its complement (target - current number) already exists in the map. This reduces the lookup time from O(n) to O(1).

### Algorithm
1. Create an empty hash map to store `{value: index}`.
2. Iterate through the array with index `i` and value `num`.
3. Compute `complement = target - num`.
4. If `complement` exists in the hash map, return `[hash_map[complement], i]`.
5. Otherwise, store `num: i` in the hash map.
6. Continue until the pair is found.

### Implementation
```python
def twoSum(nums, target):
    num_map = {}
    for i, num in enumerate(nums):
        complement = target - num
        if complement in num_map:
            return [num_map[complement], i]
        num_map[num] = i
    return []
```

### Complexity Analysis
- **Time Complexity**: O(n) -- Single pass through the array. Each hash map lookup is O(1) on average.
- **Space Complexity**: O(n) -- In the worst case, we store all n elements in the hash map before finding the pair.

## Edge Cases
| Edge Case | Expected Behavior | Why It Matters |
|-----------|-------------------|----------------|
| Two identical numbers sum to target (e.g., `[3,3]`, target=6) | Return both indices `[0,1]` | Ensures we handle duplicates correctly; we insert after checking complement |
| Negative numbers (e.g., `[-1,-2,-3,-4,-5]`, target=-8) | Return correct indices | Ensures algorithm works with negative values |
| Array of length 2 | Return `[0,1]` if they sum to target | Minimum valid input size |
| Large values near integer limits | Return correct indices | Ensures no overflow issues in complement calculation |
| Target is 0 with positive and negative pair | Return correct indices | Ensures complement logic works for zero target |

## Related Problems
| Problem | Relationship | Difficulty |
|---------|-------------|------------|
| Three Sum (LeetCode #15) | Extension: find three numbers that sum to target | Medium |
| Two Sum II - Input Array Is Sorted (LeetCode #167) | Variant: sorted input allows two-pointer approach | Medium |
| Four Sum (LeetCode #18) | Extension: find four numbers that sum to target | Medium |
| Two Sum III - Data Structure Design (LeetCode #170) | Variant: design a data structure supporting add and find | Easy |
| Subarray Sum Equals K (LeetCode #560) | Related: uses hash map for cumulative sum lookups | Medium |

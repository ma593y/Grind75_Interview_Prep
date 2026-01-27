# Two Sum - Study Guide

## Problem Summary
- **Difficulty**: Easy
- **Category**: Array
- **Tags**: Array, Hash Table
- **LeetCode Link**: https://leetcode.com/problems/two-sum/

> **One-liner**: Find two indices in an array whose corresponding values add up to a given target.

## Key Intuition
For each number, we know exactly what partner it needs: `target - num`. Instead of scanning the entire array to find that partner (O(n) per element), we can remember every number we have seen so far in a hash map. This transforms a search problem into a lookup problem, cutting time from O(n^2) to O(n).

## Approaches Overview
| Approach | Time | Space | Trade-off |
|----------|------|-------|-----------|
| Brute Force (nested loops) | O(n^2) | O(1) | Simple but slow for large inputs |
| Hash Map (one pass) | O(n) | O(n) | Optimal time by trading extra space |

## Approach 1: Brute Force
### Core Idea
Check every possible pair of numbers using two nested loops. If any pair sums to the target, return their indices.

### Code
```python
def twoSum(nums, target):
    for i in range(len(nums)):
        for j in range(i + 1, len(nums)):
            if nums[i] + nums[j] == target:
                return [i, j]
    return []
```

### Walkthrough
```
nums = [2, 7, 11, 15], target = 9

i=0: nums[0]=2
  j=1: 2 + 7 = 9 == target --> return [0, 1]
```

## Approach 2: Hash Map (Optimal)
### Core Idea
As we iterate, store each number and its index in a dictionary. Before storing, check if the complement (`target - num`) already exists in the dictionary. If it does, we found our pair.

### Code
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

### Walkthrough
```
nums = [2, 7, 11, 15], target = 9

i=0, num=2: complement = 9-2 = 7, not in map. map = {2:0}
i=1, num=7: complement = 9-7 = 2, 2 IS in map at index 0. Return [0, 1]
```

## Complexity Analysis
- **Time**: O(n) -- We traverse the list once. Each lookup and insertion in the hash map is O(1) average.
- **Space**: O(n) -- We store up to n elements in the hash map before finding the answer.

## Common Mistakes
1. **Using the same element twice**: Forgetting that you cannot pair an element with itself. The one-pass approach avoids this by checking the complement before inserting the current element.
2. **Returning values instead of indices**: The problem asks for indices, not the values themselves. Make sure to store and return indices.
3. **Not handling duplicates**: If `nums = [3, 3]` and `target = 6`, both indices must be returned. The one-pass approach handles this naturally since we check complement before inserting.
4. **Two-pass vs one-pass confusion**: A two-pass approach (build map first, then search) works but requires extra care to avoid pairing an element with itself. The one-pass approach is cleaner.

## Interview Tips
- **How to communicate**: Start by confirming inputs/outputs: "So I receive an array of integers and a target, and I need to return the two indices that sum to the target. There is exactly one solution and I cannot use the same element twice."
- **Clarifying questions to ask**:
  - Can the array contain negative numbers? (Yes)
  - Can there be duplicate values? (Yes)
  - Is the array sorted? (No -- if yes, two-pointer would work)
  - Is there always exactly one solution? (Yes, per the problem)
- **Optimization path**: "The brute force checks all pairs in O(n^2). We can optimize by noting that for each element, we know exactly what complement we need. Using a hash map lets us check in O(1) whether we have seen that complement, giving us O(n) total time."
- **Follow-up variations**:
  - What if the array is sorted? (Use two pointers for O(1) space)
  - What if there could be multiple valid pairs? (Return all pairs)
  - What about Three Sum? (Sort + two pointers, or hash map)

## Mnemonics / Memory Aids
- **"Complement Check"**: For each number, ask "Have I seen my complement before?" This single question drives the entire algorithm.
- **"Store then check" vs "Check then store"**: Remember to check FIRST, then store. This prevents pairing an element with itself.
- **"Trade space for time"**: The classic hash map tradeoff. O(n) space buys us O(n) time instead of O(n^2).

## Self-Assessment Checklist
- [ ] Can I identify this problem type within 2 minutes?
- [ ] Can I code the optimal solution without looking at notes?
- [ ] Can I explain the time/space complexity clearly?
- [ ] Can I handle all edge cases?
- [ ] Can I discuss trade-offs between approaches?

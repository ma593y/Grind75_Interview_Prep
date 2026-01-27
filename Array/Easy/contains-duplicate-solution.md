# Contains Duplicate

## Problem Information
- **Difficulty**: Easy
- **Category**: Array
- **Tags**: Array, Hash Table, Sorting
- **LeetCode Link**: https://leetcode.com/problems/contains-duplicate/

## Problem Statement

Given an integer array `nums`, return `true` if any value appears **at least twice** in the array, and return `false` if every element is distinct.

### Examples

**Example 1:**
```
Input: nums = [1, 2, 3, 1]
Output: true
Explanation: The element 1 appears at index 0 and index 3.
```

**Example 2:**
```
Input: nums = [1, 2, 3, 4]
Output: false
Explanation: All elements are distinct.
```

**Example 3:**
```
Input: nums = [1, 1, 1, 3, 3, 4, 3, 2, 4, 2]
Output: true
```

### Constraints

- `1 <= nums.length <= 10^5`
- `-10^9 <= nums[i] <= 10^9`

## Core Concept / Pattern

This problem is fundamentally about detecting duplicates in a collection. The most natural data structure for duplicate detection is a **hash set**, which provides O(1) average-case lookup. The key insight is that instead of comparing every pair of elements (which is O(n^2)), we can track elements we have already seen and check each new element against that record.

An alternative approach uses **sorting**: if we sort the array first, any duplicate elements will be adjacent, allowing a single linear scan to detect them. This trades the extra space of a hash set for the O(n log n) cost of sorting.

## Approach 1: Brute Force

### Intuition

The most straightforward approach is to check every pair of elements. For each element, compare it against every other element to see if there is a match. If any two elements are equal, we have found a duplicate.

### Algorithm

1. Use two nested loops.
2. The outer loop picks each element at index `i`.
3. The inner loop checks all elements at indices `j > i`.
4. If `nums[i] == nums[j]`, return `True`.
5. If no duplicate is found after all comparisons, return `False`.

### Implementation

```python
def containsDuplicate(nums: list[int]) -> bool:
    n = len(nums)
    for i in range(n):
        for j in range(i + 1, n):
            if nums[i] == nums[j]:
                return True
    return False
```

### Complexity Analysis

- **Time Complexity**: O(n^2) -- We compare every pair of elements. The number of pairs is n*(n-1)/2.
- **Space Complexity**: O(1) -- No extra data structures are used.

### Why We Can Do Better

Checking all pairs is wasteful because we re-examine elements many times. By leveraging a hash set, we can remember which elements we have already seen, reducing the problem to a single pass through the array.

## Approach 2: Optimal Solution (Hash Set)

### Intuition

As we iterate through the array, we maintain a set of elements we have encountered so far. Before processing each element, we check if it already exists in the set. If it does, we have found a duplicate. If not, we add it to the set and continue. Since set lookup and insertion are both O(1) on average, the entire process takes O(n) time.

### Algorithm

1. Initialize an empty set `seen`.
2. For each element `num` in `nums`:
   - If `num` is already in `seen`, return `True`.
   - Otherwise, add `num` to `seen`.
3. If the loop completes without finding a duplicate, return `False`.

### Implementation

```python
def containsDuplicate(nums: list[int]) -> bool:
    seen = set()
    for num in nums:
        if num in seen:
            return True
        seen.add(num)
    return False
```

**Alternative one-liner:**

```python
def containsDuplicate(nums: list[int]) -> bool:
    return len(nums) != len(set(nums))
```

### Complexity Analysis

- **Time Complexity**: O(n) -- We traverse the array once. Each set lookup and insertion is O(1) on average.
- **Space Complexity**: O(n) -- In the worst case (no duplicates), the set stores all n elements.

## Approach 3: Sorting

### Intuition

If we sort the array, duplicate elements must be adjacent. A single linear scan comparing consecutive elements will reveal any duplicates.

### Implementation

```python
def containsDuplicate(nums: list[int]) -> bool:
    nums.sort()
    for i in range(1, len(nums)):
        if nums[i] == nums[i - 1]:
            return True
    return False
```

### Complexity Analysis

- **Time Complexity**: O(n log n) -- Dominated by the sorting step.
- **Space Complexity**: O(1) or O(n) -- Depends on the sorting algorithm (in-place vs. not).

## Edge Cases

| Edge Case | Input | Expected Output | Why It Matters |
|---|---|---|---|
| Single element | `[1]` | `false` | Cannot have duplicates with one element |
| Two identical elements | `[1, 1]` | `true` | Minimum duplicate case |
| All same elements | `[5, 5, 5, 5]` | `true` | Duplicate detected immediately |
| Large range of values | `[-10^9, 10^9]` | `false` | Ensure no overflow issues |
| Empty array | `[]` | `false` | Boundary condition (though constraints say length >= 1) |

## Related Problems

| Problem | Difficulty | How It Relates |
|---|---|---|
| [Two Sum](https://leetcode.com/problems/two-sum/) | Easy | Also uses hash map for O(n) lookup |
| [Contains Duplicate II](https://leetcode.com/problems/contains-duplicate-ii/) | Easy | Adds a distance constraint on duplicates |
| [Contains Duplicate III](https://leetcode.com/problems/contains-duplicate-iii/) | Hard | Adds both distance and value-difference constraints |
| [Single Number](https://leetcode.com/problems/single-number/) | Easy | Finding uniqueness instead of duplicates |
| [Missing Number](https://leetcode.com/problems/missing-number/) | Easy | Detecting presence/absence in a collection |

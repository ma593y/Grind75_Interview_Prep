# Majority Element

## Problem Information
- **Difficulty**: Easy
- **Category**: Array
- **Tags**: Array, Hash Table, Divide and Conquer, Sorting, Counting
- **LeetCode Link**: https://leetcode.com/problems/majority-element/

## Problem Statement

Given an array `nums` of size `n`, return the **majority element**. The majority element is the element that appears more than `n / 2` times. You may assume that the majority element always exists in the array.

### Examples

**Example 1:**
```
Input: nums = [3, 2, 3]
Output: 3
Explanation: 3 appears 2 times out of 3 elements (2 > 3/2).
```

**Example 2:**
```
Input: nums = [2, 2, 1, 1, 1, 2, 2]
Output: 2
Explanation: 2 appears 4 times out of 7 elements (4 > 7/2).
```

### Constraints

- `n == nums.length`
- `1 <= n <= 5 * 10^4`
- `-10^9 <= nums[i] <= 10^9`

**Follow-up**: Could you solve the problem in linear time and in O(1) space?

## Core Concept / Pattern

The majority element appears more than n/2 times, which means it occurs more frequently than all other elements combined. This powerful property enables **Boyer-Moore Voting Algorithm**, which treats the problem like an election: the majority candidate can "survive" being cancelled out by all other elements because it has more votes. The algorithm maintains a candidate and a counter, incrementing when we see the candidate and decrementing otherwise. The candidate that survives to the end must be the majority element.

## Approach 1: Brute Force (Hash Map Counting)

### Intuition

Count the frequency of each element using a hash map. The element whose count exceeds n/2 is the majority element. This is straightforward and easy to understand.

### Algorithm

1. Create a hash map to store the count of each element.
2. Iterate through the array, incrementing the count for each element.
3. Return the element whose count exceeds `n // 2`.

### Implementation

```python
from collections import Counter

def majorityElement(nums: list[int]) -> int:
    counts = Counter(nums)
    majority_threshold = len(nums) // 2
    for num, count in counts.items():
        if count > majority_threshold:
            return num
```

**Alternative without Counter:**

```python
def majorityElement(nums: list[int]) -> int:
    counts = {}
    majority_threshold = len(nums) // 2
    for num in nums:
        counts[num] = counts.get(num, 0) + 1
        if counts[num] > majority_threshold:
            return num
```

### Complexity Analysis

- **Time Complexity**: O(n) -- Single pass through the array plus iterating over at most n distinct elements.
- **Space Complexity**: O(n) -- Hash map can store up to n distinct elements.

### Why We Can Do Better

While the hash map approach is already O(n) time, it uses O(n) space. The follow-up asks for O(1) space. The Boyer-Moore Voting Algorithm achieves both O(n) time and O(1) space.

## Approach 2: Optimal Solution (Boyer-Moore Voting Algorithm)

### Intuition

Imagine each element as a voter. The majority candidate has more than half the votes. If we pair up each "majority vote" with a "non-majority vote," the majority candidate will still have votes remaining because it has more than n/2 occurrences. The algorithm simulates this cancellation process: maintain a `candidate` and a `count`. When we see the candidate, increment the count. When we see a different element, decrement the count. When the count reaches zero, pick a new candidate. The majority element will always be the final candidate.

### Algorithm

1. Initialize `candidate = None` and `count = 0`.
2. For each element `num` in `nums`:
   - If `count == 0`, set `candidate = num`.
   - If `num == candidate`, increment `count`.
   - Else, decrement `count`.
3. Return `candidate`.

### Implementation

```python
def majorityElement(nums: list[int]) -> int:
    candidate = None
    count = 0

    for num in nums:
        if count == 0:
            candidate = num
        count += 1 if num == candidate else -1

    return candidate
```

### Complexity Analysis

- **Time Complexity**: O(n) -- Single pass through the array.
- **Space Complexity**: O(1) -- Only two variables regardless of input size.

## Approach 3: Sorting

### Intuition

If the array is sorted, the majority element must be at the middle index `n // 2`, because it occupies more than half the positions.

### Implementation

```python
def majorityElement(nums: list[int]) -> int:
    nums.sort()
    return nums[len(nums) // 2]
```

### Complexity Analysis

- **Time Complexity**: O(n log n) -- Sorting step.
- **Space Complexity**: O(1) -- If sorting in-place.

## Edge Cases

| Edge Case | Input | Expected Output | Why It Matters |
|---|---|---|---|
| Single element | `[1]` | `1` | Trivially the majority |
| All same elements | `[5, 5, 5]` | `5` | Every element is the majority |
| Two elements | `[1, 1]` | `1` | Minimum case with a clear majority |
| Majority at edges | `[1, 2, 1]` | `1` | Majority element is not contiguous |
| Large array | `[1]*50001` | `1` | Performance test |

## Related Problems

| Problem | Difficulty | How It Relates |
|---|---|---|
| [Majority Element II](https://leetcode.com/problems/majority-element-ii/) | Medium | Find elements appearing more than n/3 times (extends Boyer-Moore) |
| [Check if a Number Is Majority Element](https://leetcode.com/problems/check-if-a-number-is-majority-element-in-a-sorted-array/) | Easy | Binary search variant on sorted arrays |
| [Top K Frequent Elements](https://leetcode.com/problems/top-k-frequent-elements/) | Medium | Generalized frequency counting |
| [Contains Duplicate](https://leetcode.com/problems/contains-duplicate/) | Easy | Also uses hash-based element tracking |

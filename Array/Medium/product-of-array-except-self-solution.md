# Product of Array Except Self

## Problem Information
- **Difficulty**: Medium
- **Category**: Array
- **Tags**: Array, Prefix Sum
- **LeetCode Link**: https://leetcode.com/problems/product-of-array-except-self/

## Problem Statement

Given an integer array `nums`, return an array `answer` such that `answer[i]` is equal to the product of all the elements of `nums` except `nums[i]`.

The product of any prefix or suffix of `nums` is **guaranteed** to fit in a **32-bit** integer.

You must write an algorithm that runs in O(n) time and **without using the division operation**.

### Examples

**Example 1:**
```
Input: nums = [1, 2, 3, 4]
Output: [24, 12, 8, 6]
Explanation:
  answer[0] = 2 * 3 * 4 = 24
  answer[1] = 1 * 3 * 4 = 12
  answer[2] = 1 * 2 * 4 = 8
  answer[3] = 1 * 2 * 3 = 6
```

**Example 2:**
```
Input: nums = [-1, 1, 0, -3, 3]
Output: [0, 0, 9, 0, 0]
```

### Constraints

- `2 <= nums.length <= 10^5`
- `-30 <= nums[i] <= 30`
- The product of any prefix or suffix of `nums` is guaranteed to fit in a 32-bit integer.

**Follow-up**: Can you solve it in O(1) extra space complexity? (The output array does not count as extra space.)

## Core Concept / Pattern

The product of all elements except `nums[i]` equals the product of all elements to the left of `i` multiplied by the product of all elements to the right of `i`. We can precompute these **prefix products** and **suffix products** in two separate passes. The clever optimization is to use the output array itself for one direction, then apply the other direction in a single variable, achieving O(1) extra space.

This is a variation of the **prefix sum** pattern, but with multiplication instead of addition.

## Approach 1: Brute Force

### Intuition

For each index `i`, multiply all elements except `nums[i]`. This requires an inner loop for each element.

### Algorithm

1. For each index `i`, iterate through all other indices `j != i`.
2. Multiply all `nums[j]` together.
3. Store the product in `answer[i]`.

### Implementation

```python
def productExceptSelf(nums: list[int]) -> list[int]:
    n = len(nums)
    answer = [1] * n
    for i in range(n):
        for j in range(n):
            if i != j:
                answer[i] *= nums[j]
    return answer
```

### Complexity Analysis

- **Time Complexity**: O(n^2) -- Nested loops.
- **Space Complexity**: O(1) -- Besides the output array.

### Why We Can Do Better

We are recomputing overlapping products. By precomputing prefix and suffix products, we can reduce to O(n) time.

## Approach 2: Optimal Solution (Prefix and Suffix Products)

### Intuition

For each index `i`:
- `left_product[i]` = product of all elements to the left of `i` (indices 0 to i-1).
- `right_product[i]` = product of all elements to the right of `i` (indices i+1 to n-1).
- `answer[i] = left_product[i] * right_product[i]`.

We can compute left products in a left-to-right pass and right products in a right-to-left pass. To achieve O(1) extra space, we store left products in the answer array, then multiply in the right products using a single running variable.

### Algorithm

1. Initialize `answer` array of size n with all 1s.
2. **Left pass**: For `i` from 0 to n-1, set `answer[i] = left_product` (running product of elements before i).
3. **Right pass**: For `i` from n-1 to 0, multiply `answer[i]` by `right_product` (running product of elements after i).
4. Return `answer`.

### Implementation

```python
def productExceptSelf(nums: list[int]) -> list[int]:
    n = len(nums)
    answer = [1] * n

    # Left pass: answer[i] = product of all elements to the left of i
    left_product = 1
    for i in range(n):
        answer[i] = left_product
        left_product *= nums[i]

    # Right pass: multiply by product of all elements to the right of i
    right_product = 1
    for i in range(n - 1, -1, -1):
        answer[i] *= right_product
        right_product *= nums[i]

    return answer
```

### Complexity Analysis

- **Time Complexity**: O(n) -- Two passes through the array.
- **Space Complexity**: O(1) -- Extra space (the output array does not count per the problem statement).

## Approach 3: Division (If Allowed)

### Intuition

Compute the total product of all elements. For each index, divide the total product by `nums[i]`. Must handle zeros carefully.

### Implementation

```python
def productExceptSelf(nums: list[int]) -> list[int]:
    n = len(nums)
    zero_count = nums.count(0)

    if zero_count > 1:
        return [0] * n

    if zero_count == 1:
        product_without_zero = 1
        for num in nums:
            if num != 0:
                product_without_zero *= num
        return [product_without_zero if num == 0 else 0 for num in nums]

    total_product = 1
    for num in nums:
        total_product *= num
    return [total_product // num for num in nums]
```

### Complexity Analysis

- **Time Complexity**: O(n)
- **Space Complexity**: O(1)

**Note**: The problem explicitly states "without using the division operation," so this approach does not satisfy the constraints but is worth knowing.

## Edge Cases

| Edge Case | Input | Expected Output | Why It Matters |
|---|---|---|---|
| Contains one zero | `[1, 2, 0, 4]` | `[0, 0, 8, 0]` | Only the zero position has non-zero product |
| Contains two zeros | `[0, 0, 1]` | `[0, 0, 0]` | All products are zero |
| Two elements | `[2, 3]` | `[3, 2]` | Minimum input size |
| Contains negatives | `[-1, 2, -3]` | `[-6, 3, -2]` | Sign handling |
| All ones | `[1, 1, 1]` | `[1, 1, 1]` | Product is always 1 |

## Related Problems

| Problem | Difficulty | How It Relates |
|---|---|---|
| [Prefix Sum](https://leetcode.com/problems/running-sum-of-1d-array/) | Easy | Same pattern with addition instead of multiplication |
| [Trapping Rain Water](https://leetcode.com/problems/trapping-rain-water/) | Hard | Uses left/right prefix max arrays |
| [Maximum Product Subarray](https://leetcode.com/problems/maximum-product-subarray/) | Medium | Product-based array DP |
| [Subarray Sum Equals K](https://leetcode.com/problems/subarray-sum-equals-k/) | Medium | Prefix sum pattern |

# Product of Array Except Self - Study Guide

## Problem Summary

> **One-liner**: Return an array where each element is the product of all other elements, without using division, in O(n) time.

## Key Intuition

The product of everything except `nums[i]` is simply the product of everything to the left of `i` times the product of everything to the right of `i`. By computing prefix products (left-to-right) and suffix products (right-to-left) in two passes, we avoid the need for division and achieve O(n) time. The space optimization stores the left products in the output array, then multiplies in the right products using a single variable.

## Approaches Overview

| Approach | Time | Space | Pros | Cons |
|---|---|---|---|---|
| Brute Force | O(n^2) | O(1) | Simple | Too slow |
| Division | O(n) | O(1) | Simple with math | Not allowed by problem |
| Two arrays (prefix/suffix) | O(n) | O(n) | Clear, easy to understand | Extra space |
| Single output array | O(n) | O(1) | Optimal time and space | Slightly tricky |

## Approach 1: Two Separate Arrays

### Core Idea

Build a `left[]` array where `left[i]` = product of elements before index `i`, and a `right[]` array where `right[i]` = product of elements after index `i`. Then `answer[i] = left[i] * right[i]`.

### Code

```python
def productExceptSelf(nums: list[int]) -> list[int]:
    n = len(nums)
    left = [1] * n
    right = [1] * n

    for i in range(1, n):
        left[i] = left[i - 1] * nums[i - 1]

    for i in range(n - 2, -1, -1):
        right[i] = right[i + 1] * nums[i + 1]

    return [left[i] * right[i] for i in range(n)]
```

### Walkthrough

Given `nums = [1, 2, 3, 4]`:

**Left products** (product of everything before i):
| Index | 0 | 1 | 2 | 3 |
|---|---|---|---|---|
| left[] | 1 | 1 | 1*2=2 | 1*2*3=6 |

**Right products** (product of everything after i):
| Index | 0 | 1 | 2 | 3 |
|---|---|---|---|---|
| right[] | 2*3*4=24 | 3*4=12 | 4 | 1 |

**Answer** = left * right:
| Index | 0 | 1 | 2 | 3 |
|---|---|---|---|---|
| answer[] | 1*24=24 | 1*12=12 | 2*4=8 | 6*1=6 |

Result: `[24, 12, 8, 6]`

## Approach 2: Single Output Array (Optimal)

### Core Idea

Use the output array to store the left products, then sweep from right to left with a running variable to multiply in the right products. This eliminates the need for extra arrays.

### Code

```python
def productExceptSelf(nums: list[int]) -> list[int]:
    n = len(nums)
    answer = [1] * n

    # Left pass
    left_product = 1
    for i in range(n):
        answer[i] = left_product
        left_product *= nums[i]

    # Right pass
    right_product = 1
    for i in range(n - 1, -1, -1):
        answer[i] *= right_product
        right_product *= nums[i]

    return answer
```

### Walkthrough

Given `nums = [1, 2, 3, 4]`:

**Left pass** (building prefix products in answer):
| i | left_product (before) | answer[i] set to | left_product (after) |
|---|---|---|---|
| 0 | 1 | 1 | 1*1=1 |
| 1 | 1 | 1 | 1*2=2 |
| 2 | 2 | 2 | 2*3=6 |
| 3 | 6 | 6 | 6*4=24 |

answer after left pass: `[1, 1, 2, 6]`

**Right pass** (multiplying suffix products into answer):
| i | right_product (before) | answer[i] *= | right_product (after) |
|---|---|---|---|
| 3 | 1 | 6*1=6 | 1*4=4 |
| 2 | 4 | 2*4=8 | 4*3=12 |
| 1 | 12 | 1*12=12 | 12*2=24 |
| 0 | 24 | 1*24=24 | 24*1=24 |

Final answer: `[24, 12, 8, 6]`

## Complexity Analysis

| Approach | Time | Space | Notes |
|---|---|---|---|
| Brute Force | O(n^2) | O(1) | Too slow for constraints |
| Division | O(n) | O(1) | Not allowed |
| Two arrays | O(n) | O(n) | Clear but uses extra space |
| Single output array | O(n) | O(1) | Optimal (output not counted) |

## Common Mistakes

1. **Forgetting the identity element**: Initialize `left_product` and `right_product` to 1 (multiplicative identity), not 0.
2. **Off-by-one in prefix/suffix**: The left product at index `i` should NOT include `nums[i]` itself. Similarly for the right product.
3. **Using division**: The problem explicitly forbids division. Even if you handle zeros, it violates the constraint.
4. **Not handling zeros**: If there is one zero, only the zero's position has a non-zero result. With two or more zeros, all results are zero.
5. **Updating the running product at the wrong time**: Update `left_product *= nums[i]` AFTER setting `answer[i]`, not before.

## Interview Tips

- **Mention the constraint**: Immediately acknowledge that division is not allowed, showing you read the problem carefully.
- **Start with two arrays**: Explain the two-array approach first for clarity, then optimize to one array.
- **Draw the prefix/suffix table**: A visual table makes the logic crystal clear to the interviewer.
- **Discuss the follow-up**: Explain how you eliminate the extra arrays by reusing the output array.
- **Handle zeros proactively**: Mention how zeros affect the problem even though the optimal solution handles them naturally.

## Mnemonics / Memory Aids

- **"Left times Right"**: `answer[i] = left_product * right_product`. Simple multiplication of what is to the left and what is to the right.
- **"Build then blend"**: First build the left products in the answer array, then blend in the right products from the opposite direction.
- **"Exclude self = left * right"**: The product excluding yourself is everything to your left times everything to your right.

## Self-Assessment Checklist

- [ ] Can I implement the two-pass solution from memory?
- [ ] Can I explain why we update the running product AFTER setting the answer?
- [ ] Can I trace through an example with zeros?
- [ ] Do I understand why the output array does not count as extra space?
- [ ] Can I explain the connection to prefix sums?
- [ ] Can I handle the edge case of multiple zeros?

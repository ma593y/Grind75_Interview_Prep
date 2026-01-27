# Add Binary

## Problem Information
- **Difficulty**: Easy
- **Category**: String
- **Tags**: Math, String, Bit Manipulation, Simulation
- **LeetCode Link**: https://leetcode.com/problems/add-binary/

## Problem Statement
Given two binary strings `a` and `b`, return their sum as a binary string.

### Examples
**Example 1:**
```
Input: a = "11", b = "1"
Output: "100"
```

**Example 2:**
```
Input: a = "1010", b = "1011"
Output: "10101"
```

### Constraints
- `1 <= a.length, b.length <= 10^4`
- `a` and `b` consist only of `'0'` or `'1'` characters.
- Each string does not contain leading zeros except for the zero itself.

## Core Concept / Pattern
This problem simulates binary addition from right to left, exactly as you would do it by hand. The key is managing the carry bit as you iterate through both strings from their least significant bit. This pattern of digit-by-digit addition with carry generalizes to adding numbers represented as strings in any base, including the classic "Add Two Numbers" linked list problem.

## Approach 1: Built-in Conversion
### Intuition
Convert both binary strings to integers, add them, and convert back to a binary string.

### Algorithm
1. Convert `a` and `b` to integers using base 2.
2. Add them.
3. Convert the sum back to a binary string.

### Implementation
```python
def addBinary(a: str, b: str) -> str:
    return bin(int(a, 2) + int(b, 2))[2:]
```

### Complexity Analysis
- **Time Complexity**: O(n + m) where n and m are the lengths of `a` and `b`. Conversion and addition each take linear time.
- **Space Complexity**: O(max(n, m)) for the result string.

### Why We Can Do Better
While this approach is concise, interviewers typically want you to simulate the addition manually. Also, for very large numbers that exceed integer limits (in languages with fixed-size integers), this approach may overflow.

## Approach 2: Simulate Binary Addition (Optimal)
### Intuition
Process both strings from right to left, adding corresponding bits along with any carry. This mirrors how you add binary numbers by hand.

### Algorithm
1. Initialize pointers at the end of both strings and a carry variable set to 0.
2. While there are digits left in either string or carry is non-zero:
   - Get the current digit from each string (0 if past the beginning).
   - Compute `total = digit_a + digit_b + carry`.
   - The current result bit is `total % 2`.
   - The new carry is `total // 2`.
3. Reverse the collected bits to form the final answer.

### Implementation
```python
def addBinary(a: str, b: str) -> str:
    result = []
    i, j = len(a) - 1, len(b) - 1
    carry = 0

    while i >= 0 or j >= 0 or carry:
        total = carry
        if i >= 0:
            total += int(a[i])
            i -= 1
        if j >= 0:
            total += int(b[j])
            j -= 1
        result.append(str(total % 2))
        carry = total // 2

    return ''.join(reversed(result))
```

### Complexity Analysis
- **Time Complexity**: O(max(n, m)) where n and m are the lengths of `a` and `b`. We process each digit exactly once.
- **Space Complexity**: O(max(n, m)) for the result string.

## Edge Cases
| Edge Case | Expected Behavior | Why It Matters |
|-----------|-------------------|----------------|
| One string is `"0"` | Return the other string | Addition with zero should be identity |
| Both strings are `"0"` | Return `"0"` | Zero plus zero is zero |
| Strings of very different lengths | Shorter string treated as having leading zeros | Must handle index bounds properly |
| Result has extra digit like `"1" + "1" = "10"` | Carry produces an extra leading 1 | Must continue the loop while carry is non-zero |
| Maximum length strings (10^4 digits) | Must work without overflow | Simulation approach handles arbitrary length |

## Related Problems
| Problem | Relationship | Difficulty |
|---------|-------------|------------|
| Add Two Numbers (LeetCode #2) | Same digit-by-digit addition pattern on linked lists | Medium |
| Plus One (LeetCode #66) | Simpler version: add 1 to a number represented as array | Easy |
| Multiply Strings (LeetCode #43) | Extends to multiplication of string-represented numbers | Medium |
| Add Strings (LeetCode #415) | Same pattern but for decimal strings | Easy |

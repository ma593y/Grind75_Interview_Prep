# Add Binary - Study Guide

## Problem Summary
- **Difficulty**: Easy
- **Category**: String
- **Tags**: Math, String, Bit Manipulation, Simulation
- **LeetCode Link**: https://leetcode.com/problems/add-binary/

> **One-liner**: Add two binary strings by simulating right-to-left addition with a carry bit.

## Key Intuition
Binary addition works exactly like decimal addition: process digits from right to left, sum corresponding digits plus carry, write down the result bit (sum % 2), and propagate the carry (sum // 2). The loop must continue as long as there are unprocessed digits in either string OR there is a remaining carry.

## Approaches Overview
| Approach | Time | Space | Trade-off |
|----------|------|-------|-----------|
| Built-in Conversion | O(n + m) | O(max(n,m)) | One-liner but may overflow in some languages |
| Simulate Addition | O(max(n,m)) | O(max(n,m)) | Interview-preferred, no overflow risk |

## Approach 1: Built-in Conversion
### Core Idea
Leverage language built-ins to convert binary strings to integers, add, and convert back.

### Code
```python
def addBinary(a: str, b: str) -> str:
    return bin(int(a, 2) + int(b, 2))[2:]
```

### Walkthrough
Using `a = "11"`, `b = "1"`:
1. `int("11", 2)` = 3, `int("1", 2)` = 1
2. 3 + 1 = 4
3. `bin(4)` = `"0b100"`, slice off `"0b"` -> `"100"`

## Approach 2: Simulate Addition (Optimal)
### Core Idea
Use two pointers starting from the rightmost digits, simulate addition with carry, build result in reverse.

### Code
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

### Walkthrough
Using `a = "1010"`, `b = "1011"`:
```
Step 1: i=3(0), j=3(1), carry=0 -> total=1, bit=1, carry=0
Step 2: i=2(1), j=2(1), carry=0 -> total=2, bit=0, carry=1
Step 3: i=1(0), j=1(0), carry=1 -> total=1, bit=1, carry=0
Step 4: i=0(1), j=0(1), carry=0 -> total=2, bit=0, carry=1
Step 5: carry=1 -> total=1, bit=1, carry=0
Result (reversed): "10101"
```

## Complexity Analysis
- **Both approaches**: O(max(n, m)) time, O(max(n, m)) space for the result.
- The simulation approach avoids integer overflow issues that fixed-size integer languages face.

## Common Mistakes
1. **Forgetting to continue while carry is non-zero**: `"1" + "1"` produces `"10"` -- the carry creates an extra digit.
2. **Processing strings left-to-right**: Binary addition goes right-to-left. You must start from the end of each string.
3. **Forgetting to reverse the result**: Since we build the result from least significant bit, it must be reversed at the end.
4. **Not handling different-length strings**: When one string is exhausted, treat missing digits as 0.

## Interview Tips
- **How to communicate**: Explain binary addition as an extension of grade-school decimal addition. Mention the carry mechanism clearly.
- **Clarifying questions to ask**: "Can the strings be empty?", "Are there leading zeros?", "What is the maximum length?"
- **Optimization path**: Mention the built-in approach as a quick solution, then pivot to simulation as the proper algorithm. Optionally discuss bit manipulation using XOR and AND for addition without arithmetic operators.
- **Follow-up variations**: "Can you do this with bit manipulation instead of arithmetic?", "What about adding decimal strings?" (LeetCode #415), "What about adding linked list representations?" (LeetCode #2).

## Mnemonics / Memory Aids
- **"Right to Left, Carry the Rest"**: Always process from the rightmost digit, carry forward.
- **"Mod for digit, Div for carry"**: `total % 2` gives the result bit, `total // 2` gives the carry. This works for any base.
- Think of it like a physical addition on paper: write down the column sum's last digit, carry the rest.

## Self-Assessment Checklist
- [ ] Can I identify this problem type within 2 minutes?
- [ ] Can I code the optimal solution without looking at notes?
- [ ] Can I explain the time/space complexity clearly?
- [ ] Can I handle all edge cases?
- [ ] Can I discuss trade-offs between approaches?

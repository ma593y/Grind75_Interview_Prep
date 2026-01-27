# Basic Calculator

## Problem Information
- **Difficulty**: Hard
- **Category**: String
- **Tags**: String, Stack, Math, Recursion
- **LeetCode Link**: https://leetcode.com/problems/basic-calculator/

## Problem Statement

Given a string `s` representing a valid mathematical expression, implement a basic calculator to evaluate it and return the result.

The expression string may contain:
- Open `(` and closing `)` parentheses
- The plus `+` or minus `-` sign
- Non-negative integers
- Empty spaces `' '`

**Note:** You may assume that the given expression is always valid. Do not use the `eval` built-in library function.

### Examples

**Example 1:**
```
Input: s = "1 + 1"
Output: 2
```

**Example 2:**
```
Input: s = " 2-1 + 2 "
Output: 3
```

**Example 3:**
```
Input: s = "(1+(4+5+2)-3)+(6+8)"
Output: 23
```

### Constraints

- `1 <= s.length <= 3 * 10^5`
- `s` consists of digits, `'+'`, `'-'`, `'('`, `')'`, and `' '`.
- `s` represents a valid expression.
- `'+'` is not used as a unary operation (i.e., `"+1"` and `"+(2 + 3)"` are invalid).
- `'-'` could be used as a unary operation (i.e., `"-1"` and `"-(2 + 3)"` are valid).
- There will be no two consecutive operators in the input.
- Every number and running calculation will fit in a signed 32-bit integer.

## Core Concept / Pattern

The key insight is using a **stack to handle parentheses**. When you encounter `(`, push the current accumulated result and sign onto the stack. When you encounter `)`, pop and combine. Between parentheses, simply process `+`, `-`, and multi-digit numbers linearly.

## Approach 1: Brute Force (Recursive Parsing)

### Intuition

Treat each parenthesized sub-expression as a recursive subproblem. Find matching parentheses, recursively evaluate inner expressions, and substitute the result back.

### Algorithm

1. Scan for the innermost pair of parentheses.
2. Evaluate the expression inside.
3. Replace the parenthesized expression with its numeric result.
4. Repeat until no parentheses remain.
5. Evaluate the final flat expression.

### Implementation

```python
class Solution:
    def calculate(self, s: str) -> int:
        # Recursive descent parser
        self.i = 0

        def parse_expr() -> int:
            result = 0
            sign = 1
            while self.i < len(s):
                ch = s[self.i]
                if ch == ' ':
                    self.i += 1
                elif ch == '+':
                    sign = 1
                    self.i += 1
                elif ch == '-':
                    sign = -1
                    self.i += 1
                elif ch == '(':
                    self.i += 1  # skip '('
                    val = parse_expr()
                    result += sign * val
                elif ch == ')':
                    self.i += 1  # skip ')'
                    return result
                else:
                    # Parse multi-digit number
                    num = 0
                    while self.i < len(s) and s[self.i].isdigit():
                        num = num * 10 + int(s[self.i])
                        self.i += 1
                    result += sign * num
            return result

        return parse_expr()
```

### Complexity Analysis
- **Time Complexity**: O(n) -- each character is visited once.
- **Space Complexity**: O(n) -- recursion depth proportional to nesting depth, worst case O(n).

### Why We Can Do Better

The recursive approach is clean but uses the call stack, which can overflow for deeply nested expressions. An iterative stack-based approach avoids this and is often preferred in interviews.

## Approach 2: Iterative Stack (Optimal)

### Intuition

Process the string left to right. Maintain a running `result` and a `sign` (initially +1). When we see a `(`, push the current `result` and `sign` onto a stack and reset them. When we see `)`, pop the saved sign and result, and combine: `result = popped_result + popped_sign * result`.

### Algorithm

1. Initialize `result = 0`, `sign = 1`, `num = 0`, and an empty `stack`.
2. Iterate through each character:
   - **Digit**: Build the multi-digit number `num = num * 10 + digit`.
   - **`+`**: Add `sign * num` to `result`, reset `num = 0`, set `sign = 1`.
   - **`-`**: Add `sign * num` to `result`, reset `num = 0`, set `sign = -1`.
   - **`(`**: Push `result` and `sign` onto stack, reset `result = 0` and `sign = 1`.
   - **`)`**: Add `sign * num` to `result`, reset `num = 0`. Pop `prev_sign` and `prev_result` from stack. Set `result = prev_result + prev_sign * result`.
   - **Space**: Skip.
3. After the loop, add any remaining `sign * num` to `result`.

### Implementation

```python
class Solution:
    def calculate(self, s: str) -> int:
        stack = []
        result = 0
        sign = 1
        num = 0

        for ch in s:
            if ch.isdigit():
                num = num * 10 + int(ch)
            elif ch == '+':
                result += sign * num
                num = 0
                sign = 1
            elif ch == '-':
                result += sign * num
                num = 0
                sign = -1
            elif ch == '(':
                # Save current state
                stack.append(result)
                stack.append(sign)
                # Reset for sub-expression
                result = 0
                sign = 1
            elif ch == ')':
                # Finalize current sub-expression
                result += sign * num
                num = 0
                # Combine with saved state
                prev_sign = stack.pop()
                prev_result = stack.pop()
                result = prev_result + prev_sign * result

        # Don't forget the last number
        result += sign * num
        return result
```

### Complexity Analysis
- **Time Complexity**: O(n) -- single pass through the string.
- **Space Complexity**: O(n) -- stack depth proportional to nesting depth, worst case O(n).

## Edge Cases

| Edge Case | Input | Expected Output | Why It Matters |
|---|---|---|---|
| Single number | `"42"` | `42` | No operators at all |
| Unary minus | `"-1"` | `-1` | Leading minus sign |
| Nested parentheses | `"((1+2))"` | `3` | Multiple levels of nesting |
| Spaces everywhere | `" 3 + 5 "` | `8` | Spaces should be ignored |
| Negation of group | `"-(3+2)"` | `-5` | Sign applies to entire group |
| Large expression | `"1+1+1+...+1"` (10^5 ones) | `100000` | Performance test |
| Subtraction chain | `"10-5-3-2"` | `0` | Left-to-right evaluation |

## Related Problems

| Problem | Difficulty | Key Similarity |
|---|---|---|
| [Basic Calculator II (LC #227)](https://leetcode.com/problems/basic-calculator-ii/) | Medium | Adds `*` and `/`, no parentheses |
| [Basic Calculator III (LC #772)](https://leetcode.com/problems/basic-calculator-iii/) | Hard | Combines `+`, `-`, `*`, `/` with parentheses |
| [Evaluate Reverse Polish Notation (LC #150)](https://leetcode.com/problems/evaluate-reverse-polish-notation/) | Medium | Stack-based expression evaluation |
| [Different Ways to Add Parentheses (LC #241)](https://leetcode.com/problems/different-ways-to-add-parentheses/) | Medium | Recursive expression evaluation |
| [Expression Add Operators (LC #282)](https://leetcode.com/problems/expression-add-operators/) | Hard | Backtracking with expression evaluation |

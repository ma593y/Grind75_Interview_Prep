# Evaluate Reverse Polish Notation - Study Guide

## Problem Summary
- **Difficulty**: Medium / **Category**: Stack / **Tags**: Array, Math, Stack / **LeetCode Link**: https://leetcode.com/problems/evaluate-reverse-polish-notation/

> **One-liner**: Evaluate an arithmetic expression given in postfix (Reverse Polish) notation using a stack.

## Key Intuition

In RPN, operands come before their operator. A stack naturally accumulates operands until an operator arrives, at which point the two most recent operands are consumed. This eliminates the need for parentheses or precedence rules. The critical detail is the pop order: the first popped value is the right operand (`b`), and the second is the left operand (`a`), so the operation is `a op b`.

## Approaches Overview

| Approach | Time | Space | Trade-off |
|----------|------|-------|-----------|
| List reduction (find and replace) | O(n^2) | O(n) | Conceptually simple but slow |
| Stack evaluation | O(n) | O(n) | Optimal single-pass solution |

## Approach 1: List Reduction

### Core Idea
Scan the token list for the first operator, apply it to its two preceding operands, replace all three with the result, and repeat until one value remains.

### Code

```python
class Solution:
    def evalRPN(self, tokens: list[str]) -> int:
        ops = {'+', '-', '*', '/'}
        tokens = list(tokens)
        while len(tokens) > 1:
            for i, token in enumerate(tokens):
                if token in ops:
                    a, b = int(tokens[i - 2]), int(tokens[i - 1])
                    if token == '+': result = a + b
                    elif token == '-': result = a - b
                    elif token == '*': result = a * b
                    else: result = int(a / b)
                    tokens = tokens[:i - 2] + [str(result)] + tokens[i + 1:]
                    break
        return int(tokens[0])
```

### Walkthrough
Tokens: `["2", "1", "+", "3", "*"]`
- Find `+` at index 2. Apply to 2, 1 -> 3. Tokens: `["3", "3", "*"]`
- Find `*` at index 2. Apply to 3, 3 -> 9. Tokens: `["9"]`
- Return 9.

## Approach 2: Stack Evaluation (Optimal)

### Core Idea
Iterate through tokens. Push numbers onto the stack. When an operator is encountered, pop two values, apply the operator (respecting operand order), and push the result back.

### Code

```python
class Solution:
    def evalRPN(self, tokens: list[str]) -> int:
        stack = []

        for token in tokens:
            if token in ('+', '-', '*', '/'):
                b = stack.pop()
                a = stack.pop()
                if token == '+':
                    stack.append(a + b)
                elif token == '-':
                    stack.append(a - b)
                elif token == '*':
                    stack.append(a * b)
                else:
                    stack.append(int(a / b))
            else:
                stack.append(int(token))

        return stack[0]
```

### Walkthrough
Tokens: `["4", "13", "5", "/", "+"]`
- `"4"`: push 4. Stack: [4]
- `"13"`: push 13. Stack: [4, 13]
- `"5"`: push 5. Stack: [4, 13, 5]
- `"/"`: pop b=5, a=13. 13/5 = 2 (truncated). Push 2. Stack: [4, 2]
- `"+"`: pop b=2, a=4. 4+2 = 6. Push 6. Stack: [6]
- Return 6.

## Complexity Analysis

- **Time**: O(n) -- Each token is visited exactly once; push and pop are O(1).
- **Space**: O(n) -- In the worst case, the stack stores about n/2 operands before the first operator appears.

## Common Mistakes

1. **Wrong operand order**: When popping, `b = pop()` first (right operand), then `a = pop()` (left operand). The operation is `a op b`. Swapping gives wrong results for subtraction and division.
2. **Using Python floor division `//` instead of truncation**: `int(a / b)` truncates toward zero (e.g., `int(-7/2) = -3`), but `//` floors (e.g., `-7 // 2 = -4`). The problem requires truncation toward zero.
3. **Not handling negative number tokens**: Tokens like `"-11"` are negative numbers, not the minus operator followed by `11`. The check `token in ('+', '-', '*', '/')` correctly handles this since `"-11"` is not in the set.
4. **Forgetting that single-token input is valid**: `["42"]` has no operators and should return `42`.

## Interview Tips

- **How to communicate**: Explain what RPN is (operators follow operands, no parentheses needed). Draw the stack state as you walk through an example. Highlight the operand order issue.
- **Clarifying questions to ask**: "Is the input always a valid RPN expression?" (Yes). "How should division behave with negative numbers?" (Truncate toward zero). "Can tokens be negative numbers?" (Yes).
- **Optimization path**: Mention list reduction as brute force O(n^2), then the standard stack approach O(n). There is no way to do better than O(n) since every token must be read.
- **Follow-up variations**: "Convert infix to postfix" (Shunting Yard algorithm). "Evaluate infix expressions" (LeetCode #224, #227). "Support more operators like exponentiation."

## Mnemonics / Memory Aids

- **"Pop B first, then A"**: The top of the stack is the right operand. `a op b` where `a` is deeper in the stack.
- **"int(a/b) not a//b"**: Truncation toward zero, not floor division. This matters for negative numbers.
- **"Numbers push, operators compute"**: Two simple rules drive the entire algorithm.

## Self-Assessment Checklist
- [ ] Can I identify this problem type within 2 minutes?
- [ ] Can I code the optimal solution without looking at notes?
- [ ] Can I explain the time/space complexity clearly?
- [ ] Can I handle all edge cases?
- [ ] Can I discuss trade-offs between approaches?

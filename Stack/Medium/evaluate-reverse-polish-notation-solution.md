# Evaluate Reverse Polish Notation

## Problem Information
- **Difficulty**: Medium
- **Category**: Stack
- **Tags**: Array, Math, Stack
- **LeetCode Link**: https://leetcode.com/problems/evaluate-reverse-polish-notation/

## Problem Statement

You are given an array of strings `tokens` that represents an arithmetic expression in Reverse Polish Notation (RPN).

Evaluate the expression and return an integer that represents the value of the expression.

**Note that:**
- The valid operators are `'+'`, `'-'`, `'*'`, and `'/'`.
- Each operand may be an integer or another expression.
- The division between two integers always **truncates toward zero**.
- There will not be any division by zero.
- The input represents a valid arithmetic expression in reverse polish notation.
- The answer and all the intermediate calculations can be represented in a **32-bit** integer.

### Examples

**Example 1:**
```
Input: tokens = ["2","1","+","3","*"]
Output: 9
Explanation: ((2 + 1) * 3) = 9
```

**Example 2:**
```
Input: tokens = ["4","13","5","/","+"]
Output: 6
Explanation: (4 + (13 / 5)) = 4 + 2 = 6
```

**Example 3:**
```
Input: tokens = ["10","6","9","3","+","-11","*","/","*","17","+","5","+"]
Output: 22
Explanation:
  ((10 * (6 / ((9 + 3) * -11))) + 17) + 5
= ((10 * (6 / (12 * -11))) + 17) + 5
= ((10 * (6 / -132)) + 17) + 5
= ((10 * 0) + 17) + 5
= (0 + 17) + 5
= 22
```

### Constraints
- `1 <= tokens.length <= 10^4`
- `tokens[i]` is either an operator (`"+"`, `"-"`, `"*"`, or `"/"`) or an integer in the range `[-200, 200]`.

## Core Concept / Pattern

Reverse Polish Notation (postfix notation) places operators after their operands, eliminating the need for parentheses and operator precedence rules. A **stack** is the natural data structure for evaluating RPN: push operands, and when an operator is encountered, pop the top two operands, apply the operator, and push the result back.

The stack always contains intermediate results and pending operands. Each operator consumes two values and produces one, so the stack shrinks by one element per operator. A valid RPN expression with `n` operands has exactly `n-1` operators, leaving exactly one value on the stack at the end.

## Approach 1: Brute Force (Simulation with List Reduction)

### Intuition

Scan the token list for an operator. When found, apply it to the two preceding operands, replace the three tokens with the result, and restart scanning. This simulates RPN evaluation without an explicit stack.

### Algorithm

1. Convert tokens to a mutable list.
2. Scan for the first operator.
3. Apply it to the two preceding values.
4. Replace the three elements (operand, operand, operator) with the result.
5. Repeat until one element remains.

### Implementation

```python
class Solution:
    def evalRPN(self, tokens: list[str]) -> int:
        ops = {'+', '-', '*', '/'}
        tokens = list(tokens)

        while len(tokens) > 1:
            for i, token in enumerate(tokens):
                if token in ops:
                    a = int(tokens[i - 2])
                    b = int(tokens[i - 1])
                    if token == '+':
                        result = a + b
                    elif token == '-':
                        result = a - b
                    elif token == '*':
                        result = a * b
                    else:
                        result = int(a / b)  # Truncate toward zero
                    tokens = tokens[:i - 2] + [str(result)] + tokens[i + 1:]
                    break

        return int(tokens[0])
```

### Complexity Analysis
- **Time Complexity**: O(n^2) -- Each scan through the list is O(n), and we perform O(n) reductions.
- **Space Complexity**: O(n) -- Storing the modified list.

### Why We Can Do Better

The repeated scanning and list rebuilding is inefficient. A stack-based approach processes each token exactly once in O(n) time.

## Approach 2: Optimal Solution (Stack)

### Intuition

Process tokens left to right. If a token is a number, push it onto the stack. If it is an operator, pop two numbers, apply the operator, and push the result. The final value on the stack is the answer.

### Algorithm

1. Initialize an empty stack.
2. For each token:
   - If it is a number, push it onto the stack.
   - If it is an operator, pop `b` (top) and `a` (second), compute `a op b`, push the result.
3. Return the single remaining element on the stack.

### Implementation

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
                    # Truncate toward zero (not floor division)
                    stack.append(int(a / b))
            else:
                stack.append(int(token))

        return stack[0]
```

### Complexity Analysis
- **Time Complexity**: O(n) -- Each token is processed exactly once with O(1) work per token.
- **Space Complexity**: O(n) -- The stack can hold up to (n+1)/2 elements (all operands before any operator).

## Edge Cases

| Edge Case | Expected Behavior | Why It Matters |
|-----------|-------------------|----------------|
| Single number `["42"]` | Returns `42` | No operators, just one operand |
| Negative numbers `["-3","2","+"]` | Returns `-1` | Tokens can be negative integers |
| Division truncates toward zero | `7 / -2 = -3` not `-4` | Python's `//` floors, but we need truncation toward zero; use `int(a/b)` |
| Large expression | Processes correctly | Tests stack can handle many intermediate values |
| All same operator | Applies left-associatively via stack order | Ensures `a` and `b` are popped in correct order |

## Related Problems

| Problem | Relationship | Difficulty |
|---------|-------------|------------|
| Basic Calculator (LeetCode #224) | Evaluates infix expressions with parentheses | Hard |
| Basic Calculator II (LeetCode #227) | Evaluates infix with `+`, `-`, `*`, `/` without parentheses | Medium |
| Valid Parentheses (LeetCode #20) | Stack-based bracket matching | Easy |
| Asteroid Collision (LeetCode #735) | Stack-based simulation with collisions | Medium |

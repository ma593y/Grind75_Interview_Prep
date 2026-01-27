# Basic Calculator - Study Guide

## Problem Summary

> **One-liner**: Evaluate a string expression containing `+`, `-`, parentheses, non-negative integers, and spaces, returning the integer result without using `eval`.

## Key Intuition

Parentheses create **nested scopes**. A stack naturally models this: push the current state when entering `(`, pop and merge when exiting `)`. Between parentheses, the expression is just a linear sequence of additions and subtractions, which you can evaluate left to right.

## Approaches Overview

| Approach | Time | Space | Key Idea |
|---|---|---|---|
| Recursive Descent Parser | O(n) | O(n) | Treat `(` as a recursive call, `)` as a return |
| Iterative Stack | O(n) | O(n) | Push/pop result and sign at parentheses boundaries |

Both approaches are O(n) time and O(n) space. The iterative stack approach avoids potential stack overflow from deep recursion and is generally preferred.

## Approach 1: Recursive Descent Parser

### Core Idea

Use a global index pointer. Define a function `parse_expr()` that reads and evaluates an expression until it hits `)` or end of string. When `(` is encountered, call `parse_expr()` recursively to evaluate the sub-expression.

### Code

```python
class Solution:
    def calculate(self, s: str) -> int:
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
                    self.i += 1
                    val = parse_expr()
                    result += sign * val
                elif ch == ')':
                    self.i += 1
                    return result
                else:
                    num = 0
                    while self.i < len(s) and s[self.i].isdigit():
                        num = num * 10 + int(s[self.i])
                        self.i += 1
                    result += sign * num
            return result

        return parse_expr()
```

### Walkthrough

Example: `s = "(1+(4+5+2)-3)+(6+8)"`

```
parse_expr() called (outer)
  sees '(' -> self.i=1, recurse
    parse_expr() (first inner group)
      num=1, result=1
      sees '+' -> sign=1
      sees '(' -> self.i=4, recurse
        parse_expr() (innermost)
          num=4, result=4
          '+' sign=1, num=5, result=9
          '+' sign=1, num=2, result=11
          ')' -> return 11
      result = 1 + 1*11 = 12
      '-' sign=-1, num=3
      result = 12 + (-1)*3 = 9
      ')' -> return 9
  result = 0 + 1*9 = 9
  '+' sign=1
  sees '(' -> recurse
    parse_expr()
      num=6, result=6
      '+' sign=1, num=8, result=14
      ')' -> return 14
  result = 9 + 1*14 = 23
return 23
```

## Approach 2: Iterative Stack (Optimal)

### Core Idea

Walk through the string maintaining `result`, `sign`, and `num`. When encountering `(`, push `result` and `sign` onto the stack and reset. When encountering `)`, finalize the sub-expression result and combine with the popped state: `result = popped_result + popped_sign * result`.

### Code

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
                stack.append(result)
                stack.append(sign)
                result = 0
                sign = 1
            elif ch == ')':
                result += sign * num
                num = 0
                prev_sign = stack.pop()
                prev_result = stack.pop()
                result = prev_result + prev_sign * result

        # Flush the last number
        result += sign * num
        return result
```

### Walkthrough

Example: `s = "2-(3+1)"`

| Step | ch | num | sign | result | stack | Action |
|------|-----|-----|------|--------|-------|--------|
| 1 | `'2'` | 2 | 1 | 0 | [] | Build number |
| 2 | `'-'` | 0 | -1 | 2 | [] | Flush 2, set sign=-1 |
| 3 | `'('` | 0 | 1 | 0 | [2, -1] | Push result=2 and sign=-1, reset |
| 4 | `'3'` | 3 | 1 | 0 | [2, -1] | Build number |
| 5 | `'+'` | 0 | 1 | 3 | [2, -1] | Flush 3 |
| 6 | `'1'` | 1 | 1 | 3 | [2, -1] | Build number |
| 7 | `')'` | 0 | 1 | -2 | [] | Flush 1 -> sub=4, pop sign=-1, pop prev=2, result=2+(-1)*4=-2 |
| End | | 0 | | -2 | [] | Flush num=0, final result=-2 |

Result: **-2**

## Complexity Analysis

| Approach | Time | Space | Notes |
|---|---|---|---|
| Recursive Descent | O(n) | O(d) where d = max nesting depth | d can be O(n) in worst case |
| Iterative Stack | O(n) | O(d) where d = max nesting depth | Same asymptotic, but avoids recursion overhead |

## Common Mistakes

1. **Forgetting to flush the last number.** After the loop ends, `num` may still hold an unprocessed value. Always add `sign * num` to `result` at the end.
2. **Incorrect stack push/pop order.** Push `result` first, then `sign`. Pop `sign` first, then `result`. Reversing this produces wrong answers.
3. **Not resetting `result` and `sign` after `(`.** The sub-expression starts fresh with `result=0` and `sign=1`.
4. **Forgetting multi-digit numbers.** Characters like `'1'`, `'2'` in sequence form `12`, not `1` and `2` separately.
5. **Not handling unary minus.** `"-1"` or `"-(3+2)"` must work. The initial `sign=1` plus the `-` operator handles this naturally.

## Interview Tips

- **Clarify the operators first.** This problem only has `+` and `-`. If the interviewer adds `*` and `/`, the approach changes (need precedence handling).
- **Walk through the stack state.** Drawing the stack contents at each `(` and `)` makes the logic crystal clear.
- **Start with the no-parentheses case.** First explain how to evaluate `"3+5-2"` linearly, then show how the stack extends this to handle parentheses.
- **Mention edge cases proactively**: unary minus, spaces, deeply nested expressions, single number with no operators.
- **Know the family.** Basic Calculator I (this problem) has `+`, `-`, `()`. Basic Calculator II adds `*`, `/` but no `()`. Basic Calculator III has all of them.

## Mnemonics / Memory Aids

- **"Push on Open, Pop on Close"** -- `(` pushes state, `)` pops state.
- **"Stack stores the OUTSIDE"** -- when entering `(`, the stack saves the result and sign from *before* the parenthesis, not inside it.
- **"Reset inside"** -- after pushing, always reset `result=0` and `sign=1` for the new sub-expression.
- **"Flush before you switch"** -- before changing sign or hitting `(` / `)`, always flush the current number with `result += sign * num; num = 0`.

## Self-Assessment Checklist

- [ ] Can I write the iterative stack solution from scratch in under 15 minutes?
- [ ] Can I trace through `"(1+(4+5+2)-3)+(6+8)"` step by step, showing the stack at each point?
- [ ] Do I correctly handle unary minus like `"-(3+2)"` ?
- [ ] Can I explain why we push both `result` AND `sign` onto the stack?
- [ ] Do I remember to flush the last number after the loop?
- [ ] Can I explain the difference between Basic Calculator I, II, and III?
- [ ] Could I extend this solution to handle `*` and `/` if asked?

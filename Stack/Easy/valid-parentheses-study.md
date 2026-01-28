# Valid Parentheses - Study Guide

## Problem Summary
- **Difficulty**: Easy / **Category**: Stack / **Tags**: String, Stack / **LeetCode Link**: https://leetcode.com/problems/valid-parentheses/

> **One-liner**: Determine if a string of brackets `()[]{}` is validly nested and matched.

## Key Intuition

A stack naturally handles nested structures. Every opening bracket gets pushed, and every closing bracket must match the most recently pushed (and unmatched) opening bracket. If at any point there is a mismatch or the stack is empty when a closing bracket appears, the string is invalid. The string is valid only if the stack is empty after processing all characters.

## Approaches Overview

| Approach | Time | Space | Trade-off |
|----------|------|-------|-----------|
| Repeated removal of pairs | O(n^2) | O(n) | Simple idea but slow due to repeated string scanning |
| Stack-based matching | O(n) | O(n) | Optimal single pass |

## Approach 1: Repeated Removal

### Core Idea
Keep replacing `()`, `[]`, `{}` substrings with empty strings until none remain. If the final string is empty, it was valid.

### Code

```python
class Solution:
    def isValid(self, s: str) -> bool:
        while '()' in s or '[]' in s or '{}' in s:
            s = s.replace('()', '')
            s = s.replace('[]', '')
            s = s.replace('{}', '')
        return s == ''
```

### Walkthrough
Using `s = "{[]}"`:
- Pass 1: `[]` found, remove it -> `"{}"`
- Pass 2: `{}` found, remove it -> `""`
- String is empty -> return `True`.

## Approach 2: Stack (Optimal)

### Core Idea
Use a dictionary mapping closing brackets to opening brackets. Iterate through the string: push opening brackets, and for closing brackets, check if the stack top matches. Pop on match; return `False` on mismatch or empty stack.

### Code

```python
class Solution:
    def isValid(self, s: str) -> bool:
        stack = []
        matching = {')': '(', ']': '[', '}': '{'}

        for char in s:
            if char in matching:
                if not stack or stack[-1] != matching[char]:
                    return False
                stack.pop()
            else:
                stack.append(char)

        return len(stack) == 0
```

### Walkthrough
Using `s = "([)]"`:
- `'('`: push -> stack = `['(']`
- `'['`: push -> stack = `['(', '[']`
- `')'`: closing, matching[')'] = '(', but stack top is '['. Mismatch! Return `False`.

Using `s = "{[]}"`:
- `'{'`: push -> stack = `['{']`
- `'['`: push -> stack = `['{', '[']`
- `']'`: closing, matching[']'] = '[', stack top is '['. Match! Pop -> stack = `['{']`
- `'}'`: closing, matching['}'] = '{', stack top is '{'. Match! Pop -> stack = `[]`
- Stack is empty -> return `True`.

## Complexity Analysis

- **Time**: O(n) -- Each character is processed exactly once with O(1) stack operations.
- **Space**: O(n) -- In the worst case (e.g., `"(((("`) all characters are pushed onto the stack.

## Common Mistakes

1. **Forgetting to check if the stack is empty before popping**: A closing bracket with an empty stack means there is no matching opener. Always check `not stack` before accessing `stack[-1]`.
2. **Checking only that brackets are balanced in count**: `"([)]"` has equal opening and closing brackets but is invalid because the nesting order is wrong.
3. **Forgetting the final empty-stack check**: `"((("` processes without error but the stack is non-empty, meaning there are unmatched opening brackets.
4. **Using the wrong mapping direction**: Map closing -> opening (not the reverse), so you can look up what the closing bracket expects.

## Interview Tips

- **How to communicate**: Explain the LIFO property of stacks and how it mirrors bracket nesting. Mention the dictionary for clean matching logic.
- **Clarifying questions to ask**: "Does the string contain only bracket characters?" (Yes per constraints). "Can the string be empty?" (No, length >= 1).
- **Optimization path**: Mention the brute force string replacement first, then explain why the stack is better (single pass, O(n)). An early exit optimization: if `len(s)` is odd, immediately return `False`.
- **Follow-up variations**: "What if we need to find the longest valid substring?" leads to LeetCode #32 (Hard). "What if we need to count minimum removals?" leads to #921.

## Mnemonics / Memory Aids

- **"Push open, pop closed"**: Opening brackets always push. Closing brackets always try to pop.
- **"Dictionary of closers"**: Map each closing bracket to its expected opening bracket: `)` -> `(`, `]` -> `[`, `}` -> `{`.
- **"Empty stack = valid string"**: After processing everything, an empty stack means every bracket was matched.

## Self-Assessment Checklist
- [ ] Can I identify this problem type within 2 minutes?
- [ ] Can I code the optimal solution without looking at notes?
- [ ] Can I explain the time/space complexity clearly?
- [ ] Can I handle all edge cases?
- [ ] Can I discuss trade-offs between approaches?

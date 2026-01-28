# Valid Parentheses

## Problem Information
- **Difficulty**: Easy
- **Category**: Stack
- **Tags**: String, Stack
- **LeetCode Link**: https://leetcode.com/problems/valid-parentheses/

## Problem Statement

Given a string `s` containing just the characters `'('`, `')'`, `'{'`, `'}'`, `'['` and `']'`, determine if the input string is valid.

An input string is valid if:
1. Open brackets must be closed by the same type of brackets.
2. Open brackets must be closed in the correct order.
3. Every close bracket has a corresponding open bracket of the same type.

### Examples

**Example 1:**
```
Input: s = "()"
Output: true
Explanation: The single pair of parentheses is properly opened and closed.
```

**Example 2:**
```
Input: s = "()[]{}"
Output: true
Explanation: All three pairs are properly opened and closed.
```

**Example 3:**
```
Input: s = "(]"
Output: false
Explanation: The opening '(' does not match the closing ']'.
```

**Example 4:**
```
Input: s = "([)]"
Output: false
Explanation: The brackets are not closed in the correct order.
```

**Example 5:**
```
Input: s = "{[]}"
Output: true
Explanation: The inner brackets '[]' are closed before the outer '{}', which is correct nesting.
```

### Constraints
- `1 <= s.length <= 10^4`
- `s` consists of parentheses only: `'()[]{}'`

## Core Concept / Pattern

This is the quintessential **stack** problem. A stack naturally handles nested structures because of its Last-In-First-Out (LIFO) property. When we encounter an opening bracket, we push it onto the stack. When we encounter a closing bracket, we check if it matches the most recent unmatched opening bracket (the top of the stack). If the string is valid, the stack will be empty at the end.

The stack mirrors the nesting depth of the brackets. Each opening bracket increases the nesting depth (push), and each closing bracket decreases it (pop). Mismatches at any point or a non-empty stack at the end indicate an invalid string.

## Approach 1: Brute Force (Repeated Removal)

### Intuition

Repeatedly find and remove adjacent matching pairs of brackets (`()`, `[]`, `{}`). If the string becomes empty, it was valid. If no more pairs can be removed but the string is non-empty, it is invalid.

### Algorithm

1. Repeatedly scan the string for adjacent matching pairs.
2. Remove each found pair.
3. Continue until no more removals are possible.
4. Return `True` if the string is empty, `False` otherwise.

### Implementation

```python
class Solution:
    def isValid(self, s: str) -> bool:
        while '()' in s or '[]' in s or '{}' in s:
            s = s.replace('()', '')
            s = s.replace('[]', '')
            s = s.replace('{}', '')
        return s == ''
```

### Complexity Analysis
- **Time Complexity**: O(n^2) -- In the worst case (like `(((...))))`), each pass removes one pair and scans the remaining string, leading to O(n) passes of O(n) work each.
- **Space Complexity**: O(n) -- String replacement creates new strings.

### Why We Can Do Better

The repeated scanning and string manipulation is inefficient. A stack allows us to process each character exactly once, giving a linear-time solution.

## Approach 2: Optimal Solution (Stack)

### Intuition

Use a stack to track unmatched opening brackets. For each character:
- If it is an opening bracket, push it onto the stack.
- If it is a closing bracket, check if the top of the stack is the matching opening bracket. If yes, pop it. If no (or the stack is empty), the string is invalid.

At the end, the string is valid if and only if the stack is empty.

### Algorithm

1. Create a mapping of closing brackets to their matching opening brackets.
2. Initialize an empty stack.
3. For each character `c` in `s`:
   - If `c` is an opening bracket, push it.
   - If `c` is a closing bracket:
     - If the stack is empty or the top does not match, return `False`.
     - Otherwise, pop the top.
4. Return `True` if the stack is empty, `False` otherwise.

### Implementation

```python
class Solution:
    def isValid(self, s: str) -> bool:
        stack = []
        matching = {')': '(', ']': '[', '}': '{'}

        for char in s:
            if char in matching:
                # It's a closing bracket
                if not stack or stack[-1] != matching[char]:
                    return False
                stack.pop()
            else:
                # It's an opening bracket
                stack.append(char)

        return len(stack) == 0
```

### Complexity Analysis
- **Time Complexity**: O(n) -- We iterate through the string once, and each push/pop operation is O(1).
- **Space Complexity**: O(n) -- In the worst case (all opening brackets), the stack holds all n characters.

## Edge Cases

| Edge Case | Expected Behavior | Why It Matters |
|-----------|-------------------|----------------|
| Single character `"("` | Returns `False` | Odd-length strings are always invalid |
| Empty-like `"()"` | Returns `True` | Simplest valid case |
| Nested `"{[]}"` | Returns `True` | Tests proper nesting |
| Interleaved `"([)]"` | Returns `False` | Brackets must be closed in correct order |
| Only closing `")))"` | Returns `False` | Stack is empty when first closing bracket appears |
| Only opening `"((("` | Returns `False` | Stack is non-empty at the end |
| Long valid string | Returns `True` | Tests performance at scale |

## Related Problems

| Problem | Relationship | Difficulty |
|---------|-------------|------------|
| Generate Parentheses (LeetCode #22) | Generates all valid combinations instead of validating | Medium |
| Longest Valid Parentheses (LeetCode #32) | Finds the longest valid substring | Hard |
| Min Add to Make Parentheses Valid (LeetCode #921) | Counts minimum additions to make valid | Medium |
| Remove Invalid Parentheses (LeetCode #301) | Removes minimum brackets to make valid | Hard |

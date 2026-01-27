# String to Integer (atoi) - Study Guide

## Problem Summary

> **One-liner**: Convert a string to a 32-bit signed integer by parsing optional leading whitespace, an optional sign, and consecutive digits, clamping the result to the range [-2^31, 2^31 - 1].

## Key Intuition

The problem is essentially a state-machine or sequential-parsing exercise. You process the string character by character in a fixed order: skip whitespace, detect a sign, then consume digits. The trickiest part is handling **overflow** before it happens -- you must check whether appending the next digit would exceed the 32-bit signed integer range.

## Approaches Overview

| Approach | Time | Space | Key Idea |
|---|---|---|---|
| Sequential Parsing | O(n) | O(1) | Walk through string once, handle whitespace -> sign -> digits |
| Deterministic Finite Automaton (DFA) | O(n) | O(1) | Use explicit state transitions for clarity |

## Approach 1: Sequential Parsing

### Core Idea

Process the string in three explicit phases:
1. **Skip leading whitespace.**
2. **Check for an optional `+` or `-` sign.**
3. **Read digits one by one**, building the result while checking for overflow before each multiplication/addition.

### Code

```python
class Solution:
    def myAtoi(self, s: str) -> int:
        INT_MAX = 2**31 - 1  # 2147483647
        INT_MIN = -(2**31)   # -2147483648

        i = 0
        n = len(s)

        # Phase 1: Skip leading whitespace
        while i < n and s[i] == ' ':
            i += 1

        # Phase 2: Optional sign
        sign = 1
        if i < n and s[i] in ('+', '-'):
            sign = -1 if s[i] == '-' else 1
            i += 1

        # Phase 3: Convert digits
        result = 0
        while i < n and s[i].isdigit():
            digit = int(s[i])

            # Overflow check BEFORE updating result
            if result > (INT_MAX - digit) // 10:
                return INT_MAX if sign == 1 else INT_MIN

            result = result * 10 + digit
            i += 1

        return sign * result
```

### Walkthrough

Example: `s = "   -42"`

| Step | i | Character | Action | result |
|------|---|-----------|--------|--------|
| 1 | 0-2 | `' '` | Skip whitespace | 0 |
| 2 | 3 | `'-'` | Set sign = -1 | 0 |
| 3 | 4 | `'4'` | result = 0*10 + 4 = 4 | 4 |
| 4 | 5 | `'2'` | result = 4*10 + 2 = 42 | 42 |
| Return | | | sign * result = -42 | -42 |

## Approach 2: Deterministic Finite Automaton (DFA) (Optimal)

### Core Idea

Model the parsing as a state machine with states: `start`, `signed`, `in_number`, `end`. Each character triggers a transition. This is conceptually cleaner and less error-prone for complex parsing.

### Code

```python
class Solution:
    def myAtoi(self, s: str) -> int:
        INT_MAX = 2**31 - 1
        INT_MIN = -(2**31)

        # States: 'start', 'signed', 'in_number', 'end'
        # Inputs: 'space', 'sign', 'digit', 'other'
        transitions = {
            'start':     {'space': 'start', 'sign': 'signed', 'digit': 'in_number', 'other': 'end'},
            'signed':    {'space': 'end',   'sign': 'end',    'digit': 'in_number', 'other': 'end'},
            'in_number': {'space': 'end',   'sign': 'end',    'digit': 'in_number', 'other': 'end'},
            'end':       {'space': 'end',   'sign': 'end',    'digit': 'end',       'other': 'end'},
        }

        state = 'start'
        sign = 1
        result = 0

        for ch in s:
            # Classify character
            if ch == ' ':
                input_type = 'space'
            elif ch in ('+', '-'):
                input_type = 'sign'
            elif ch.isdigit():
                input_type = 'digit'
            else:
                input_type = 'other'

            state = transitions[state][input_type]

            if state == 'signed':
                sign = -1 if ch == '-' else 1
            elif state == 'in_number':
                digit = int(ch)
                if result > (INT_MAX - digit) // 10:
                    return INT_MAX if sign == 1 else INT_MIN
                result = result * 10 + digit
            elif state == 'end':
                break

        return sign * result
```

### Walkthrough

Example: `s = "4193 with words"`

| Step | ch | input_type | state | sign | result |
|------|----|-----------|-------|------|--------|
| 1 | `'4'` | digit | in_number | 1 | 4 |
| 2 | `'1'` | digit | in_number | 1 | 41 |
| 3 | `'9'` | digit | in_number | 1 | 419 |
| 4 | `'3'` | digit | in_number | 1 | 4193 |
| 5 | `' '` | space | end | 1 | 4193 |
| Return | | | | | 4193 |

## Complexity Analysis

| Approach | Time Complexity | Space Complexity |
|---|---|---|
| Sequential Parsing | O(n) | O(1) |
| DFA | O(n) | O(1) |

Both approaches iterate through the string at most once. The DFA uses a constant-size transition table, so space is O(1).

## Common Mistakes

1. **Forgetting the overflow check.** You must detect overflow *before* `result = result * 10 + digit`, not after.
2. **Incorrect overflow formula.** The check is `result > (INT_MAX - digit) // 10`. Using `>=` or omitting `digit` leads to subtle bugs.
3. **Handling multiple signs.** `"+-12"` should return `0`. Only the first sign character should be consumed.
4. **Stopping at non-digit.** Once a non-digit character is found after the digit phase, stop immediately -- do not skip over it.
5. **Empty or whitespace-only input.** Should return `0`.

## Interview Tips

- **Clarify constraints first**: Ask about the integer range (32-bit signed), handling of leading/trailing characters, and what constitutes valid input.
- **Walk through phases explicitly**: Whitespace -> Sign -> Digits -> Stop. Interviewers love seeing structured thinking.
- **Overflow detection**: Explain the math behind `result > (INT_MAX - digit) // 10` clearly. This is the crux of the problem.
- **Mention the DFA approach**: Even if you code the sequential version, mentioning the state-machine approach shows depth. It is the preferred method for more complex parsers.
- **Edge cases to mention**: empty string, only whitespace, only a sign, leading zeros like `"0032"`, values exceeding INT_MAX/INT_MIN.

## Mnemonics / Memory Aids

- **"WSD" = Whitespace, Sign, Digits** -- the three phases in order.
- **"Check Before You Wreck"** -- always check for overflow *before* updating the result.
- **INT_MAX = 2^31 - 1 = 2,147,483,647** -- memorize this value; it appears constantly in interviews.
- **The overflow guard**: `result > (MAX - digit) / 10` -- rearranged from `result * 10 + digit > MAX` to avoid the overflow itself.

## Self-Assessment Checklist

- [ ] Can I write the sequential parsing solution from scratch in under 10 minutes?
- [ ] Can I explain the overflow check formula and why it must happen before the multiplication?
- [ ] Can I trace through edge cases: `"   "`, `"+-12"`, `"words and 987"`, `"91283472332"`?
- [ ] Do I know the 32-bit signed integer range by heart?
- [ ] Can I describe the DFA approach and when it would be preferable?
- [ ] Can I identify which phase of parsing a given test input will fail at?

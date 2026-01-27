# String to Integer (atoi)

## Problem Information
- **Difficulty**: Medium
- **Category**: String
- **Tags**: String, Parsing, Math
- **LeetCode Link**: https://leetcode.com/problems/string-to-integer-atoi/

## Problem Statement
Implement the `myAtoi(string s)` function, which converts a string to a 32-bit signed integer.

The algorithm for `myAtoi(string s)` is as follows:

1. **Whitespace**: Ignore any leading whitespace (`" "`).
2. **Signedness**: Determine the sign by checking if the next character is `'-'` or `'+'`, assuming positivity if neither is present.
3. **Conversion**: Read the integer by skipping leading zeros and collecting digits until a non-digit character is encountered or the end of string is reached. If no digits were read, the result is 0.
4. **Rounding**: If the integer is out of the 32-bit signed integer range `[-2^31, 2^31 - 1]`, clamp it to the nearest boundary.

### Examples
**Example 1:**
```
Input: s = "42"
Output: 42
```

**Example 2:**
```
Input: s = "   -042"
Output: -42
Explanation: Leading whitespace is ignored and leading zeros are skipped.
```

**Example 3:**
```
Input: s = "1337c0d3"
Output: 1337
Explanation: Reading stops at 'c' which is not a digit.
```

**Example 4:**
```
Input: s = "0-1"
Output: 0
Explanation: Reading stops at '-' which is not a digit (0 was already read).
```

**Example 5:**
```
Input: s = "words and 987"
Output: 0
Explanation: The first non-whitespace character is 'w', which is not a digit or sign. Reading stops immediately.
```

### Constraints
- `0 <= s.length <= 200`
- `s` consists of English letters (lower-case and upper-case), digits (`0-9`), `' '`, `'+'`, `'-'`, and `'.'`.

## Core Concept / Pattern
This problem is about careful string parsing with multiple sequential rules. The challenge is not in any complex algorithm but in handling all the edge cases correctly: whitespace, signs, overflow, non-digit characters, and empty input. It tests your ability to write clean, sequential parsing logic and handle integer overflow gracefully.

## Approach 1: Sequential Parsing (Step-by-Step)
### Intuition
Follow the specification exactly: skip whitespace, handle sign, collect digits, and clamp to 32-bit bounds.

### Algorithm
1. Strip leading whitespace.
2. Check for an optional sign character.
3. Read digits one by one, building the number.
4. After each digit, check for overflow and clamp if needed.
5. Apply the sign and return.

### Implementation
```python
def myAtoi(s: str) -> int:
    INT_MAX = 2**31 - 1  # 2147483647
    INT_MIN = -2**31     # -2147483648

    i = 0
    n = len(s)

    # Step 1: Skip whitespace
    while i < n and s[i] == ' ':
        i += 1

    # Step 2: Handle sign
    sign = 1
    if i < n and s[i] in ('+', '-'):
        sign = -1 if s[i] == '-' else 1
        i += 1

    # Step 3: Convert digits
    result = 0
    while i < n and s[i].isdigit():
        digit = int(s[i])

        # Step 4: Check overflow before adding digit
        if result > (INT_MAX - digit) // 10:
            return INT_MAX if sign == 1 else INT_MIN

        result = result * 10 + digit
        i += 1

    return sign * result
```

### Complexity Analysis
- **Time Complexity**: O(n) where n is the length of the string. We scan through the string once.
- **Space Complexity**: O(1) since we only use a few variables.

### Why We Can Do Better
This is already optimal. However, we can also implement this as a finite state machine for a more structured approach.

## Approach 2: Finite State Machine
### Intuition
Model the parsing process as a deterministic finite automaton (DFA) with states: START, SIGNED, IN_NUMBER, and END. Each character transitions between states. This approach is more maintainable and extensible.

### Algorithm
1. Define states and transitions.
2. Process each character according to the current state.
3. Accumulate the number during the IN_NUMBER state.
4. Clamp the result to 32-bit bounds.

### Implementation
```python
class StateMachine:
    def __init__(self):
        self.state = "start"
        self.sign = 1
        self.result = 0
        self.INT_MAX = 2**31 - 1
        self.INT_MIN = -2**31
        # Transition table: state -> {char_type: next_state}
        self.transitions = {
            "start":     {"space": "start", "sign": "signed", "digit": "number", "other": "end"},
            "signed":    {"space": "end",   "sign": "end",    "digit": "number", "other": "end"},
            "number":    {"space": "end",   "sign": "end",    "digit": "number", "other": "end"},
            "end":       {"space": "end",   "sign": "end",    "digit": "end",    "other": "end"},
        }

    def _char_type(self, ch):
        if ch == ' ':
            return "space"
        if ch in ('+', '-'):
            return "sign"
        if ch.isdigit():
            return "digit"
        return "other"

    def process(self, ch):
        char_type = self._char_type(ch)
        self.state = self.transitions[self.state][char_type]

        if self.state == "signed":
            self.sign = -1 if ch == '-' else 1
        elif self.state == "number":
            digit = int(ch)
            if self.result > (self.INT_MAX - digit) // 10:
                self.result = self.INT_MAX if self.sign == 1 else -self.INT_MIN
                self.state = "end"
            else:
                self.result = self.result * 10 + digit

    def get_result(self):
        return self.sign * self.result


def myAtoi(s: str) -> int:
    machine = StateMachine()
    for ch in s:
        machine.process(ch)
        if machine.state == "end":
            break
    return machine.get_result()
```

### Complexity Analysis
- **Time Complexity**: O(n) where n is the length of the string.
- **Space Complexity**: O(1) for the state machine variables.

## Edge Cases
| Edge Case | Expected Behavior | Why It Matters |
|-----------|-------------------|----------------|
| Empty string `""` | Return `0` | No digits to parse |
| Only whitespace `"   "` | Return `0` | No digits after whitespace |
| Overflow positive `"2147483648"` | Return `2147483647` | Clamp to INT_MAX |
| Overflow negative `"-2147483649"` | Return `-2147483648` | Clamp to INT_MIN |
| Leading zeros `"-000123"` | Return `-123` | Zeros are valid digits but do not change value |
| Sign without digits `"+"` | Return `0` | No digits follow the sign |
| Non-digit after sign `"-abc"` | Return `0` | Sign present but no digits |

## Related Problems
| Problem | Relationship | Difficulty |
|---------|-------------|------------|
| Reverse Integer (LeetCode #7) | Also requires overflow handling | Medium |
| Valid Number (LeetCode #65) | More complex string parsing / validation | Hard |
| Integer to Roman (LeetCode #12) | Number to string conversion | Medium |

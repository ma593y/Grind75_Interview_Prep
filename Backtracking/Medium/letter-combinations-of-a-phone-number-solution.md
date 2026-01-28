# Letter Combinations of a Phone Number

## Problem Information
- **Difficulty**: Medium
- **Category**: Backtracking
- **Tags**: String, Backtracking, Hash Table, Recursion
- **LeetCode Link**: https://leetcode.com/problems/letter-combinations-of-a-phone-number/

## Problem Statement

Given a string containing digits from `2-9` inclusive, return all possible letter combinations that the number could represent. Return the answer in **any order**.

A mapping of digits to letters (just like on the telephone buttons) is given below. Note that 1 does not map to any letters.

```
2 -> "abc"    3 -> "def"    4 -> "ghi"
5 -> "jkl"    6 -> "mno"    7 -> "pqrs"
8 -> "tuv"    9 -> "wxyz"
```

### Examples

**Example 1:**
```
Input: digits = "23"
Output: ["ad","ae","af","bd","be","bf","cd","ce","cf"]
```

**Example 2:**
```
Input: digits = ""
Output: []
```

**Example 3:**
```
Input: digits = "2"
Output: ["a","b","c"]
```

### Constraints
- `0 <= digits.length <= 4`
- `digits[i]` is a digit in the range `['2', '9']`.

## Core Concept / Pattern

This is a **backtracking** problem where each digit maps to a set of possible characters. For each digit, we branch into all possible letters, building combinations character by character. The total number of combinations is the product of the number of letters for each digit (e.g., 3 * 3 = 9 for "23").

## Approach 1: Brute Force (Iterative / Cartesian Product)

### Intuition
Start with an empty list of combinations. For each digit, take every existing partial combination and extend it with each possible letter for the current digit.

### Algorithm
1. Initialize result with `[""]`.
2. For each digit in the input:
   - Create new combinations by appending each mapped letter to each existing combination.
   - Replace result with the new combinations.
3. Return result (or empty list if digits is empty).

### Implementation

```python
class Solution:
    def letterCombinations(self, digits: str) -> list[str]:
        if not digits:
            return []

        phone_map = {
            '2': 'abc', '3': 'def', '4': 'ghi', '5': 'jkl',
            '6': 'mno', '7': 'pqrs', '8': 'tuv', '9': 'wxyz'
        }

        result = ['']
        for digit in digits:
            letters = phone_map[digit]
            result = [combo + letter for combo in result for letter in letters]

        return result
```

### Complexity Analysis
- **Time Complexity**: O(4^N * N) where N is the length of digits. In the worst case, each digit maps to 4 letters (7 and 9). Building each string takes O(N).
- **Space Complexity**: O(4^N * N) for storing all combinations.

### Why We Can Do Better
This iterative approach works well and has the same complexity as backtracking. However, the backtracking approach is more generalizable and is the expected solution pattern in interviews.

## Approach 2: Optimal Solution (Backtracking)

### Intuition
Build each combination one character at a time using recursion. At each level of recursion, we process the next digit and try all its possible letters. When the combination reaches the same length as the input, we have a complete combination.

### Algorithm
1. Create the digit-to-letters mapping.
2. Define `backtrack(index, path)`:
   - If `len(path) == len(digits)`, add the combination to results.
   - Get the letters for `digits[index]`.
   - For each letter, append it to path and recurse with `index + 1`.
3. Handle the empty input case.

### Implementation

```python
class Solution:
    def letterCombinations(self, digits: str) -> list[str]:
        if not digits:
            return []

        phone_map = {
            '2': 'abc', '3': 'def', '4': 'ghi', '5': 'jkl',
            '6': 'mno', '7': 'pqrs', '8': 'tuv', '9': 'wxyz'
        }

        result = []

        def backtrack(index: int, path: list[str]) -> None:
            if index == len(digits):
                result.append(''.join(path))
                return

            for letter in phone_map[digits[index]]:
                path.append(letter)
                backtrack(index + 1, path)
                path.pop()

        backtrack(0, [])
        return result
```

**Alternative (using string concatenation):**

```python
class Solution:
    def letterCombinations(self, digits: str) -> list[str]:
        if not digits:
            return []

        phone_map = {
            '2': 'abc', '3': 'def', '4': 'ghi', '5': 'jkl',
            '6': 'mno', '7': 'pqrs', '8': 'tuv', '9': 'wxyz'
        }

        result = []

        def backtrack(index: int, current: str) -> None:
            if index == len(digits):
                result.append(current)
                return

            for letter in phone_map[digits[index]]:
                backtrack(index + 1, current + letter)

        backtrack(0, '')
        return result
```

### Complexity Analysis
- **Time Complexity**: O(4^N * N) where N is the number of digits. At most 4 branches per digit, and each combination has length N.
- **Space Complexity**: O(N) for the recursion stack (excluding output).

## Edge Cases

| Edge Case | Expected Behavior | Why It Matters |
|-----------|-------------------|----------------|
| Empty string `""` | Return `[]` | Must handle explicitly, not `[""]` |
| Single digit `"2"` | Return `["a","b","c"]` | Base case |
| Digits with 4 letters `"79"` | 4*4 = 16 combinations | 7 and 9 map to 4 letters each |
| Maximum length `"2345"` | 3*3*3*3 = 81 combinations | Tests completeness |

## Related Problems

| Problem | Relationship | Difficulty |
|---------|-------------|------------|
| Generate Parentheses (LC #22) | Backtracking with constraints | Medium |
| Combination Sum (LC #39) | Backtracking with sum target | Medium |
| Binary Watch (LC #401) | Mapping digits to combinations | Easy |
| Permutations (LC #46) | Similar backtracking template | Medium |

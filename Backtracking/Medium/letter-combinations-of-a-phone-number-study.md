# Letter Combinations of a Phone Number - Study Guide

## Problem Summary
> **One-liner**: Map each digit (2-9) to its phone letters and return all possible letter combinations.

## Key Intuition

Each digit maps to 3-4 letters. For a sequence of digits, we need every possible combination -- one letter per digit. This is like a tree where each level represents a digit and branches represent the letter choices. The total combinations are the product of letter counts for each digit.

## Approaches Overview

| Approach | Time | Space | Trade-off |
|----------|------|-------|-----------|
| Iterative (Cartesian Product) | O(4^N * N) | O(4^N * N) | Simple, no recursion |
| Backtracking (Recursive) | O(4^N * N) | O(N) stack | Standard interview pattern |

## Approach 1: Iterative (Cartesian Product)

### Core Idea
Start with `[""]`, and for each digit, extend every existing string with every possible letter for that digit.

### Code

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

### Walkthrough
Using `digits = "23"`:
```
Start: ['']
Process '2' (abc): ['a', 'b', 'c']
Process '3' (def): ['ad','ae','af','bd','be','bf','cd','ce','cf']
```
Each step multiplies the result size by the number of letters.

## Approach 2: Backtracking (Optimal)

### Core Idea
Recursively build each combination. At level `index`, try each letter mapped to `digits[index]`, append it, recurse to the next level, then backtrack.

### Code

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

### Walkthrough
Using `digits = "23"`:
```
backtrack(0, [])
  letter='a': backtrack(1, ['a'])
    letter='d': backtrack(2, ['a','d']) -> record "ad"
    letter='e': backtrack(2, ['a','e']) -> record "ae"
    letter='f': backtrack(2, ['a','f']) -> record "af"
  letter='b': backtrack(1, ['b'])
    letter='d': record "bd"
    letter='e': record "be"
    letter='f': record "bf"
  letter='c': backtrack(1, ['c'])
    letter='d': record "cd"
    letter='e': record "ce"
    letter='f': record "cf"
```
Result: ["ad","ae","af","bd","be","bf","cd","ce","cf"]

## Complexity Analysis

| Aspect | Value |
|--------|-------|
| Time | O(4^N * N) where N = number of digits |
| Space | O(N) recursion stack (excluding output) |
| Total combinations | Product of letter counts per digit |
| Worst case (all 7s/9s) | 4^N combinations |
| Best case (all other digits) | 3^N combinations |

## Common Mistakes

1. **Returning `[""]` instead of `[]` for empty input**: When digits is empty, return an empty list, not a list containing the empty string.
2. **Hardcoding the mapping wrong**: Double-check that 7 maps to "pqrs" (4 letters) and 9 maps to "wxyz" (4 letters).
3. **Forgetting to handle digit '1' or '0'**: The constraints say 2-9, but if encountered, they map to no letters.
4. **Using string concatenation in a list-based approach**: Either use list + join (efficient) or string concatenation (simpler but creates new strings).

## Interview Tips

- **Define the mapping clearly**: Write out the phone_map dictionary first. This shows organization.
- **Handle the empty case first**: `if not digits: return []` prevents edge case bugs.
- **Know both approaches**: The iterative approach is concise; the backtracking approach is more generalizable.
- **Discuss complexity**: Mention that 4^N is the worst case because digits 7 and 9 have 4 letters.
- **This is a great warm-up backtracking problem**: It is simpler than combination sum or word search because there is no pruning needed -- every combination is valid.

## Mnemonics / Memory Aids

- **"Phone Tree"**: Visualize each digit as a level in a tree with 3-4 branches (letters).
- **"2=abc, 3=def, ... 7=pqrs, 9=wxyz"**: Remember that 7 and 9 are special with 4 letters.
- **"Cartesian Product"**: This problem is literally the Cartesian product of letter sets for each digit.

## Self-Assessment Checklist

- [ ] Can I write the digit-to-letter mapping from memory?
- [ ] Can I implement the backtracking solution from scratch?
- [ ] Can I implement the iterative solution?
- [ ] Do I handle the empty string input correctly?
- [ ] Can I explain why the time complexity is O(4^N * N)?
- [ ] Can I trace through the recursion tree for "23"?
- [ ] Do I understand how this relates to the Cartesian product?

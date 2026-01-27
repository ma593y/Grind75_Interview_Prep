# Valid Palindrome

## Problem Information
- **Difficulty**: Easy
- **Category**: String
- **Tags**: Two Pointers, String
- **LeetCode Link**: https://leetcode.com/problems/valid-palindrome/

## Problem Statement
A phrase is a palindrome if, after converting all uppercase letters into lowercase letters and removing all non-alphanumeric characters, it reads the same forward and backward. Alphanumeric characters include letters and numbers.

Given a string `s`, return `true` if it is a palindrome, or `false` otherwise.

### Examples
**Example 1:**
```
Input: s = "A man, a plan, a canal: Panama"
Output: true
Explanation: "amanaplanacanalpanama" is a palindrome.
```

**Example 2:**
```
Input: s = "race a car"
Output: false
Explanation: "raceacar" is not a palindrome.
```

**Example 3:**
```
Input: s = " "
Output: true
Explanation: s is an empty string "" after removing non-alphanumeric characters.
Since an empty string reads the same forward and backward, it is a palindrome.
```

### Constraints
- `1 <= s.length <= 2 * 10^5`
- `s` consists only of printable ASCII characters.

## Core Concept / Pattern
This problem tests your ability to handle string cleaning and comparison. The core insight is that you can either (1) clean the string first and then compare, or (2) use two pointers to compare characters in-place while skipping non-alphanumeric characters. The two-pointer approach is more space-efficient because it avoids creating a new cleaned string.

## Approach 1: Clean and Compare
### Intuition
The simplest approach is to first strip out all non-alphanumeric characters, convert to lowercase, and then check if the resulting string equals its reverse.

### Algorithm
1. Filter the string to keep only alphanumeric characters.
2. Convert to lowercase.
3. Compare the cleaned string with its reverse.

### Implementation
```python
def isPalindrome(s: str) -> bool:
    cleaned = ''.join(ch.lower() for ch in s if ch.isalnum())
    return cleaned == cleaned[::-1]
```

### Complexity Analysis
- **Time Complexity**: O(n) where n is the length of the string. We iterate through the string once to clean it and once to reverse and compare.
- **Space Complexity**: O(n) for storing the cleaned string and its reverse.

### Why We Can Do Better
We are allocating extra space to store the cleaned string and its reverse. We can avoid this by using two pointers that skip non-alphanumeric characters in place.

## Approach 2: Two Pointers (Optimal)
### Intuition
Place one pointer at the beginning and one at the end of the string. Move them inward, skipping non-alphanumeric characters, and compare the characters at each step. If all compared pairs match, the string is a palindrome.

### Algorithm
1. Initialize `left = 0` and `right = len(s) - 1`.
2. While `left < right`:
   - Skip non-alphanumeric characters by advancing `left` or decrementing `right`.
   - Compare `s[left].lower()` with `s[right].lower()`.
   - If they differ, return `False`.
   - Otherwise, move both pointers inward.
3. Return `True`.

### Implementation
```python
def isPalindrome(s: str) -> bool:
    left, right = 0, len(s) - 1
    while left < right:
        while left < right and not s[left].isalnum():
            left += 1
        while left < right and not s[right].isalnum():
            right -= 1
        if s[left].lower() != s[right].lower():
            return False
        left += 1
        right -= 1
    return True
```

### Complexity Analysis
- **Time Complexity**: O(n) where n is the length of the string. Each character is visited at most once.
- **Space Complexity**: O(1) since we only use two pointer variables.

## Edge Cases
| Edge Case | Expected Behavior | Why It Matters |
|-----------|-------------------|----------------|
| Empty string or only spaces | Return `true` | After cleaning, empty string is a palindrome |
| Single character | Return `true` | A single character is always a palindrome |
| All non-alphanumeric characters | Return `true` | Cleaning yields empty string |
| Mixed case like `"Aa"` | Return `true` | Case-insensitive comparison is required |
| String with only numbers like `"121"` | Return `true` | Numbers are alphanumeric and must be handled |

## Related Problems
| Problem | Relationship | Difficulty |
|---------|-------------|------------|
| Valid Palindrome II (LeetCode #680) | Extension: allow at most one character removal | Easy |
| Palindrome Linked List (LeetCode #234) | Same concept applied to linked lists | Easy |
| Longest Palindromic Substring (LeetCode #5) | Finding palindromes within a string | Medium |

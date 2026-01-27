# Word Break

## Problem Information
- **Difficulty**: Medium
- **Category**: Dynamic Programming
- **Tags**: Hash Table, String, Dynamic Programming, Trie, Memoization
- **LeetCode Link**: https://leetcode.com/problems/word-break/

## Problem Statement
Given a string `s` and a dictionary of strings `wordDict`, return `true` if `s` can be segmented into a space-separated sequence of one or more dictionary words.

Note that the same word in the dictionary may be reused multiple times in the segmentation.

### Examples
**Example 1:**
```
Input: s = "leetcode", wordDict = ["leet", "code"]
Output: true
Explanation: "leetcode" can be segmented as "leet code".
```

**Example 2:**
```
Input: s = "applepenapple", wordDict = ["apple", "pen"]
Output: true
Explanation: "applepenapple" can be segmented as "apple pen apple".
Note that you are allowed to reuse a dictionary word.
```

**Example 3:**
```
Input: s = "catsandog", wordDict = ["cats", "dog", "sand", "and", "cat"]
Output: false
```

### Constraints
- `1 <= s.length <= 300`
- `1 <= wordDict.length <= 1000`
- `1 <= wordDict[i].length <= 20`
- `s` and `wordDict[i]` consist of only lowercase English letters.
- All the strings of `wordDict` are unique.

## Core Concept / Pattern
This is a string segmentation problem solved with DP. Define `dp[i]` as whether the substring `s[0:i]` can be segmented using dictionary words. For each position `i`, check all possible last words: if `dp[j]` is true and `s[j:i]` is in the dictionary, then `dp[i]` is true.

## Approach 1: Brute Force (Backtracking)
### Intuition
Try every possible prefix of the string. If a prefix is a dictionary word, recursively check if the remaining suffix can be segmented.

### Algorithm
1. If the string is empty, return true.
2. Try every prefix `s[0:i]` for i from 1 to len(s).
3. If the prefix is in the dictionary and the recursive call on the suffix returns true, return true.
4. If no prefix works, return false.

### Implementation
```python
def wordBreak(s: str, wordDict: list[str]) -> bool:
    word_set = set(wordDict)

    def backtrack(start):
        if start == len(s):
            return True
        for end in range(start + 1, len(s) + 1):
            if s[start:end] in word_set and backtrack(end):
                return True
        return False

    return backtrack(0)
```

### Complexity Analysis
- **Time Complexity**: O(2^n) -- in the worst case, every partition is tried.
- **Space Complexity**: O(n) -- recursion stack depth.

### Why We Can Do Better
Many starting positions are revisited multiple times. Memoization or bottom-up DP avoids redundant computation.

## Approach 2: Optimal Solution (Bottom-Up DP)
### Intuition
Build a boolean array `dp` where `dp[i]` means `s[0:i]` can be segmented. For each position, check all possible word endings.

### Algorithm
1. Convert `wordDict` to a set for O(1) lookup.
2. Create `dp` array of size `len(s) + 1`, all false. Set `dp[0] = True`.
3. For each `i` from 1 to `len(s)`:
   - For each `j` from 0 to `i`:
     - If `dp[j]` is true and `s[j:i]` is in the word set, set `dp[i] = True` and break.
4. Return `dp[len(s)]`.

### Implementation
```python
def wordBreak(s: str, wordDict: list[str]) -> bool:
    word_set = set(wordDict)
    dp = [False] * (len(s) + 1)
    dp[0] = True

    for i in range(1, len(s) + 1):
        for j in range(i):
            if dp[j] and s[j:i] in word_set:
                dp[i] = True
                break

    return dp[len(s)]
```

### Complexity Analysis
- **Time Complexity**: O(n^2 * m) where n is the length of `s` and m is the max word length (for substring hashing). With a good hash, effectively O(n^2).
- **Space Complexity**: O(n) for the dp array.

## Edge Cases
| Edge Case | Expected Behavior | Why It Matters |
|-----------|-------------------|----------------|
| Entire string is one dictionary word | Return true | Direct match |
| No possible segmentation | Return false | Must not loop forever |
| Single character string in dict | Return true | Minimal input |
| Reusing the same word multiple times | Must handle correctly | "aaa" with dict ["a"] -> true |
| Dictionary words that are prefixes of each other | Must try all options | "cars" with ["car", "ca", "rs"] -> true |

## Related Problems
| Problem | Relationship | Difficulty |
|---------|-------------|------------|
| Word Break II (LC #140) | Return all valid segmentations | Hard |
| Concatenated Words (LC #472) | Words formed by concatenating others | Hard |
| Palindrome Partitioning (LC #131) | Partition string with a different validity check | Medium |
| Longest Word in Dictionary (LC #720) | Build words from dictionary | Medium |

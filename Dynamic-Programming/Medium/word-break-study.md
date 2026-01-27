# Word Break - Study Guide

## Problem Summary
- **Difficulty**: Medium
- **Category**: Dynamic Programming
- **Tags**: Hash Table, String, Dynamic Programming, Trie, Memoization
- **LeetCode Link**: https://leetcode.com/problems/word-break/

> **One-liner**: Determine if a string can be segmented into space-separated words that all exist in a given dictionary.

## Key Intuition
Think of the string as a sequence of positions. Position `i` is "reachable" if there exists some earlier reachable position `j` such that `s[j:i]` is a dictionary word. Position 0 is the starting point (always reachable). The answer is whether position `len(s)` is reachable.

## Approaches Overview
| Approach | Time | Space | Trade-off |
|----------|------|-------|-----------|
| Backtracking | O(2^n) | O(n) | Exponential, tries all partitions |
| Top-Down Memoization | O(n^2) | O(n) | Caches results per starting index |
| Bottom-Up DP | O(n^2) | O(n) | Iterative, clean and optimal |

## Approach 1: Backtracking
### Core Idea
Try every prefix. If it is a word, recurse on the remaining suffix.

### Code
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

### Walkthrough
For `s = "leetcode"`, `wordDict = ["leet", "code"]`:
- backtrack(0): try "l", "le", "lee", "leet" -- "leet" is in dict, call backtrack(4)
- backtrack(4): try "c", "co", "cod", "code" -- "code" is in dict, call backtrack(8)
- backtrack(8): start == len(s), return True

## Approach 2: Bottom-Up DP (Optimal)
### Core Idea
`dp[i] = True` if `s[0:i]` can be segmented. For each `i`, check all `j < i` where `dp[j]` is true and `s[j:i]` is in the dictionary.

### Code
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

### Walkthrough
For `s = "leetcode"`, `wordDict = ["leet", "code"]`:
```
dp[0] = True  (base case)
dp[1]: check s[0:1]="l" -> not in dict. dp[1] = False
dp[2]: check s[0:2]="le" -> not in dict. dp[2] = False
dp[3]: check s[0:3]="lee" -> not in dict. dp[3] = False
dp[4]: check s[0:4]="leet" -> in dict and dp[0]=True. dp[4] = True
dp[5]: no valid split. dp[5] = False
dp[6]: no valid split. dp[6] = False
dp[7]: no valid split. dp[7] = False
dp[8]: check j=4: s[4:8]="code" -> in dict and dp[4]=True. dp[8] = True
```
Return True.

## Complexity Analysis
| Approach | Time | Space | Explanation |
|----------|------|-------|-------------|
| Backtracking | O(2^n) | O(n) | Exponential partitions |
| Bottom-Up DP | O(n^2) | O(n) | Nested loop over positions |

## Common Mistakes
1. **Not using a set for the dictionary**: Using a list for lookups makes each check O(m*k) instead of O(m).
2. **Forgetting dp[0] = True**: The empty prefix must be reachable as the base case.
3. **Not breaking early**: Once `dp[i]` is set to true, no need to check further `j` values for that `i`.
4. **Confusing indices**: `dp[i]` corresponds to `s[0:i]`, not `s[0:i-1]`. Off-by-one errors are common.

## Interview Tips
- **How to communicate**: Frame it as "can I reach the end of the string by jumping word-lengths from reachable positions?"
- **Clarifying questions to ask**: Can words be reused? Are all words lowercase? Can the string or dictionary be empty?
- **Optimization path**: "Brute force tries all 2^n partitions. DP reduces this to O(n^2) by caching reachability at each position."
- **Follow-up variations**: Word Break II (return all segmentations), using a Trie for the dictionary.

## Mnemonics / Memory Aids
- **"Reach forward from reachable spots"**: If position j is reachable and s[j:i] is a word, then position i is reachable.
- **"dp[0] = True, the rest build up"**: Base case is the empty string, everything else is derived.
- **"Set for speed"**: Always convert wordDict to a set first.

## Self-Assessment Checklist
- [ ] Can I identify this as a string segmentation DP problem?
- [ ] Can I write the bottom-up DP solution without notes?
- [ ] Can I trace through an example correctly?
- [ ] Can I explain why backtracking is exponential?
- [ ] Can I discuss the follow-up (Word Break II)?

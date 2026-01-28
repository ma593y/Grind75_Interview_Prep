# Word Ladder

## Problem Information
- **Difficulty**: Hard
- **Category**: Graph
- **Tags**: Hash Table, String, BFS, Shortest Path
- **LeetCode Link**: https://leetcode.com/problems/word-ladder/

## Problem Statement
A **transformation sequence** from word `beginWord` to word `endWord` using a dictionary `wordList` is a sequence of words `beginWord -> s1 -> s2 -> ... -> sk` such that:
- Every adjacent pair of words differs by a single letter.
- Every `si` for `1 <= i <= k` is in `wordList`. Note that `beginWord` does not need to be in `wordList`.
- `sk == endWord`

Given two words, `beginWord` and `endWord`, and a dictionary `wordList`, return the **number of words** in the **shortest transformation sequence** from `beginWord` to `endWord`, or `0` if no such sequence exists.

### Examples
**Example 1:**
```
Input: beginWord = "hit", endWord = "cog", wordList = ["hot","dot","dog","lot","log","cog"]
Output: 5
Explanation: "hit" -> "hot" -> "dot" -> "dog" -> "cog" (5 words in the sequence)
```

**Example 2:**
```
Input: beginWord = "hit", endWord = "cog", wordList = ["hot","dot","dog","lot","log"]
Output: 0
Explanation: The endWord "cog" is not in wordList, so no valid transformation exists.
```

### Constraints
- `1 <= beginWord.length <= 10`
- `endWord.length == beginWord.length`
- `1 <= wordList.length <= 5000`
- `wordList[i].length == beginWord.length`
- `beginWord`, `endWord`, and `wordList[i]` consist of lowercase English letters.
- `beginWord != endWord`
- All the words in `wordList` are **unique**.

## Core Concept / Pattern
This is a shortest path problem in an unweighted graph. Each word is a node, and an edge exists between two words that differ by exactly one letter. BFS finds the shortest path in an unweighted graph. The key optimization is how we find neighbors: instead of comparing every pair of words (O(n^2 * L)), we use wildcard patterns (e.g., "h*t" for "hit") to group words that can transform into each other.

## Approach 1: Brute Force (BFS with Pairwise Comparison)
### Intuition
Build a graph by comparing all pairs of words. Two words are neighbors if they differ by exactly one character. Then run BFS from beginWord to endWord.

### Algorithm
1. Add beginWord to the word set if not present.
2. For each pair of words, check if they differ by exactly one character.
3. Build an adjacency list.
4. BFS from beginWord. Return the path length when endWord is reached.

### Implementation
```python
from typing import List
from collections import deque

class Solution:
    def ladderLength(self, beginWord: str, endWord: str, wordList: List[str]) -> int:
        if endWord not in wordList:
            return 0

        word_set = set(wordList)
        word_set.add(beginWord)
        words = list(word_set)

        def one_diff(w1: str, w2: str) -> bool:
            return sum(c1 != c2 for c1, c2 in zip(w1, w2)) == 1

        # Build adjacency list
        graph = {word: [] for word in words}
        for i in range(len(words)):
            for j in range(i + 1, len(words)):
                if one_diff(words[i], words[j]):
                    graph[words[i]].append(words[j])
                    graph[words[j]].append(words[i])

        # BFS
        queue = deque([(beginWord, 1)])
        visited = {beginWord}

        while queue:
            word, length = queue.popleft()
            if word == endWord:
                return length
            for neighbor in graph[word]:
                if neighbor not in visited:
                    visited.add(neighbor)
                    queue.append((neighbor, length + 1))

        return 0
```

### Complexity Analysis
- **Time Complexity**: O(n^2 * L) for building the graph, where n is the number of words and L is the word length. BFS is O(n + edges).
- **Space Complexity**: O(n^2) for the adjacency list in the worst case.

### Why We Can Do Better
Instead of comparing all pairs (O(n^2 * L)), we can generate all possible one-letter mutations for each word (26 * L possibilities per word) and look them up in a set. This is O(n * L * 26) which is much better when n is large.

## Approach 2: Optimal Solution (BFS with Wildcard Patterns)
### Intuition
For each word during BFS, generate all possible one-letter mutations. Check if each mutation exists in the word set. This avoids building the full graph upfront and is faster when the word list is large relative to 26 * L.

### Algorithm
1. If endWord is not in wordList, return 0.
2. Convert wordList to a set for O(1) lookup.
3. BFS from beginWord. For each word, try replacing each character with 'a' to 'z'.
4. If a mutation is in the word set and not visited, add it to the queue.
5. Return the path length when endWord is found.

### Implementation
```python
from typing import List
from collections import deque

class Solution:
    def ladderLength(self, beginWord: str, endWord: str, wordList: List[str]) -> int:
        if endWord not in wordList:
            return 0

        word_set = set(wordList)
        queue = deque([(beginWord, 1)])
        visited = {beginWord}

        while queue:
            word, length = queue.popleft()

            if word == endWord:
                return length

            for i in range(len(word)):
                for c in 'abcdefghijklmnopqrstuvwxyz':
                    next_word = word[:i] + c + word[i + 1:]
                    if next_word in word_set and next_word not in visited:
                        visited.add(next_word)
                        queue.append((next_word, length + 1))

        return 0
```

### Complexity Analysis
- **Time Complexity**: O(n * L * 26) = O(n * L) where n is the number of words and L is the word length. Each word is dequeued once, and for each word we try 26 * L mutations.
- **Space Complexity**: O(n * L) for the word set and visited set.

## Edge Cases
| Edge Case | Expected Behavior | Why It Matters |
|-----------|-------------------|----------------|
| endWord not in wordList | Return 0 | Quick exit |
| beginWord == endWord | Not possible per constraints | beginWord != endWord guaranteed |
| No valid path exists | Return 0 | BFS exhausts all reachable words |
| Direct transformation (one step) | Return 2 | beginWord and endWord differ by 1 letter |
| Single character words | Works fine | 26 mutations per word |
| Large wordList, short words | Mutation approach is faster | O(n * 26 * L) vs O(n^2 * L) |

## Related Problems
| Problem | Relationship | Difficulty |
|---------|-------------|------------|
| Word Ladder II (#126) | Same problem but return all shortest paths | Hard |
| Minimum Genetic Mutation (#433) | Same BFS pattern with 4 characters instead of 26 | Medium |
| Open the Lock (#752) | BFS shortest path with state transitions | Medium |
| Shortest Path in Binary Matrix (#1091) | BFS shortest path on grid | Medium |

# Word Ladder - Study Guide

## Problem Summary
- **Difficulty**: Hard
- **Category**: Graph
- **Tags**: Hash Table, String, BFS, Shortest Path
- **LeetCode Link**: https://leetcode.com/problems/word-ladder/

> **One-liner**: Find the shortest transformation sequence from beginWord to endWord where each step changes exactly one letter, using BFS on an implicit word graph.

## Key Intuition
Each word is a node in a graph. Two words are connected if they differ by exactly one letter. The shortest transformation sequence is the shortest path in this unweighted graph, which BFS finds optimally. The critical optimization is generating neighbors efficiently: instead of comparing all word pairs, mutate each character position to all 26 letters and check membership in a set.

## Approaches Overview
| Approach | Time | Space | Trade-off |
|----------|------|-------|-----------|
| BFS with Pairwise Comparison | O(n^2 * L) | O(n^2) | Builds full graph, slow for large n |
| BFS with Character Mutation | O(n * L * 26) | O(n * L) | No graph construction, optimal |
| Bidirectional BFS | O(n * L * 26) but ~2x faster | O(n * L) | Searches from both ends, reduces explored space |

(n = wordList size, L = word length)

## Approach 1: BFS with Pairwise Comparison
### Core Idea
Build an explicit graph by checking all word pairs. Two words are neighbors if they differ by one character. Run BFS from beginWord to endWord.

### Code
```python
from collections import deque

class Solution:
    def ladderLength(self, beginWord: str, endWord: str, wordList: List[str]) -> int:
        if endWord not in wordList:
            return 0

        word_set = set(wordList)
        word_set.add(beginWord)
        words = list(word_set)

        def one_diff(w1, w2):
            return sum(c1 != c2 for c1, c2 in zip(w1, w2)) == 1

        graph = {w: [] for w in words}
        for i in range(len(words)):
            for j in range(i + 1, len(words)):
                if one_diff(words[i], words[j]):
                    graph[words[i]].append(words[j])
                    graph[words[j]].append(words[i])

        queue = deque([(beginWord, 1)])
        visited = {beginWord}
        while queue:
            word, length = queue.popleft()
            if word == endWord:
                return length
            for nei in graph[word]:
                if nei not in visited:
                    visited.add(nei)
                    queue.append((nei, length + 1))
        return 0
```

### Walkthrough
beginWord="hit", endWord="cog", wordList=["hot","dot","dog","lot","log","cog"]:
- Graph: hit<->hot, hot<->dot, hot<->lot, dot<->dog, dot<->lot, dog<->log, dog<->cog, lot<->log, log<->cog
- BFS: hit(1) -> hot(2) -> dot(3), lot(3) -> dog(4), lot already visited -> cog(5)
- Answer: **5**

## Approach 2: BFS with Character Mutation (Optimal)
### Core Idea
During BFS, for each word, generate all 26*L possible one-letter mutations. Check each against the word set. No explicit graph needed.

### Code
```python
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

### Walkthrough
beginWord="hit", endWord="cog":
- BFS from "hit" (length 1):
  - Mutate position 0: ait, bit, ..., hot (in set!) -> enqueue ("hot", 2)
  - Mutate position 1: hat, hbt, ..., hut -> none in set except "hot" already visited
  - Mutate position 2: hia, hib, ..., hiz -> none in set
- Process "hot" (length 2):
  - Mutate: dot (found!), lot (found!) -> enqueue both with length 3
- Process "dot" (length 3):
  - Mutate: dog (found!) -> enqueue ("dog", 4)
- Process "lot" (length 3):
  - Mutate: log (found!) -> enqueue ("log", 4)
- Process "dog" (length 4):
  - Mutate: cog (found!) -> enqueue ("cog", 5)
- Process "cog" (length 5): word == endWord -> return **5**

## Complexity Analysis
- **Pairwise**: O(n^2 * L) time for graph construction. O(n + E) for BFS.
- **Mutation**: O(n * 26 * L) = O(n * L) time. Each word processed once, 26 * L mutations per word.
- **Space**: O(n * L) for the word set and visited set.
- When n is large (5000) and L is small (10), mutation is much faster: 5000 * 260 = 1.3M vs 5000^2 * 10 = 250M.

## Common Mistakes
1. **Forgetting to check if endWord is in wordList**: Must return 0 immediately if not.
2. **Not adding beginWord to visited before BFS**: This causes beginWord to be re-enqueued.
3. **Counting edges instead of nodes**: The answer is the number of words in the sequence, not the number of transformations. If the path is beginWord -> w1 -> endWord, the answer is 3.
4. **Comparing all pairs when mutation is available**: The mutation approach is almost always faster for this problem's constraints.
5. **Modifying the word set during BFS**: Some implementations remove words from the set instead of using a visited set. This works but is less clean.

## Interview Tips
- **How to communicate**: "This is a shortest path problem in an unweighted graph. Each word is a node. I use BFS. For neighbor generation, I mutate each character to a-z and check membership in O(1)."
- **Clarifying questions**: "Is beginWord guaranteed to be in wordList?" (No, but it does not need to be.) "Are all words the same length?" (Yes.)
- **Optimization to mention**: Bidirectional BFS searches from both beginWord and endWord simultaneously. It terminates when the two frontiers meet, roughly halving the search space.
- **Why BFS, not DFS**: BFS finds the shortest path in an unweighted graph. DFS would find a path but not necessarily the shortest one.

## Mnemonics / Memory Aids
- **"Mutate, Check, Enqueue"**: For each word, mutate each position to a-z, check if the mutation exists, enqueue if valid.
- **"Nodes = words, edges = one-letter difference"**: The graph model.
- **"Count words, not steps"**: The answer counts the total words in the sequence (path length + 1 if counting edges).

## Self-Assessment Checklist
- [ ] Can I model this as a graph problem and explain the node/edge structure?
- [ ] Can I implement the character mutation neighbor generation?
- [ ] Can I trace BFS step by step and count the correct path length?
- [ ] Can I explain why mutation (26*L) is better than pairwise (n*L)?
- [ ] Can I describe bidirectional BFS as a follow-up optimization?

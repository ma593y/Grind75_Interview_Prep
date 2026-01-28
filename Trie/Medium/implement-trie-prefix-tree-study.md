# Implement Trie (Prefix Tree) - Study Guide

## Problem Summary
- **Difficulty**: Medium / **Category**: Trie / **Tags**: Hash Table, String, Design, Trie / **LeetCode Link**: https://leetcode.com/problems/implement-trie-prefix-tree/

> **One-liner**: Implement a trie data structure supporting insert, exact search, and prefix search in O(m) time per operation.

## Key Intuition

A trie stores strings as paths in a tree, where each edge is a character. Shared prefixes share the same path, making prefix lookups extremely efficient. Each node holds a dictionary of children and a boolean flag `is_end` marking whether the path from root to that node represents a complete inserted word. The difference between `search` and `startsWith` is simply whether we check `is_end` at the terminal node.

## Approaches Overview

| Approach | Time (insert/search) | Time (startsWith) | Space | Trade-off |
|----------|---------------------|-------------------|-------|-----------|
| Hash Set | O(m) | O(n * m) | O(n * m) | startsWith is slow |
| Trie (dict-based nodes) | O(m) | O(m) | O(n * m) worst | Optimal for all operations |
| Trie (array-based nodes) | O(m) | O(m) | O(n * m * 26) worst | Faster lookups, more memory |

## Approach 1: Hash Set (Baseline)

### Core Idea
Store words in a Python set. `search` is O(1) average. `startsWith` must iterate all words to find a match.

### Code

```python
class Trie:
    def __init__(self):
        self.words = set()

    def insert(self, word: str) -> None:
        self.words.add(word)

    def search(self, word: str) -> bool:
        return word in self.words

    def startsWith(self, prefix: str) -> bool:
        for word in self.words:
            if word.startswith(prefix):
                return True
        return False
```

### Walkthrough
Insert "apple", "app". `startsWith("ap")` scans both words: "apple".startswith("ap") is True, returns True. If 10,000 words stored, worst case scans all 10,000.

## Approach 2: Trie with Dictionary Nodes (Optimal)

### Core Idea
Each node stores a dictionary mapping characters to child nodes, plus an `is_end` flag. Insert creates nodes along the path. Search and startsWith traverse the path, differing only in whether `is_end` is checked.

### Code

```python
class TrieNode:
    def __init__(self):
        self.children = {}
        self.is_end = False


class Trie:
    def __init__(self):
        self.root = TrieNode()

    def insert(self, word: str) -> None:
        node = self.root
        for char in word:
            if char not in node.children:
                node.children[char] = TrieNode()
            node = node.children[char]
        node.is_end = True

    def search(self, word: str) -> bool:
        node = self._find_node(word)
        return node is not None and node.is_end

    def startsWith(self, prefix: str) -> bool:
        return self._find_node(prefix) is not None

    def _find_node(self, prefix: str):
        node = self.root
        for char in prefix:
            if char not in node.children:
                return None
            node = node.children[char]
        return node
```

### Walkthrough
Insert "apple":
- root -> 'a' (create) -> 'p' (create) -> 'p' (create) -> 'l' (create) -> 'e' (create, is_end=True)

Insert "app":
- root -> 'a' (exists) -> 'p' (exists) -> 'p' (exists, set is_end=True)

search("apple"):
- Traverse a -> p -> p -> l -> e. Node exists and is_end=True. Return True.

search("app"):
- Traverse a -> p -> p. Node exists and is_end=True (was set during insert("app")). Return True.

search("ap"):
- Traverse a -> p. Node exists but is_end=False. Return False.

startsWith("app"):
- Traverse a -> p -> p. Node exists (non-None). Return True.

startsWith("b"):
- root has no child 'b'. Return None -> False.

## Complexity Analysis

- **Time**: O(m) for all operations -- where m is the length of the word or prefix. Each character requires one dictionary lookup (O(1) average).
- **Space**: O(n * m) in the worst case -- if no words share prefixes, every character of every word creates a new node. In practice, shared prefixes significantly reduce space.

## Common Mistakes

1. **Forgetting `is_end` flag**: Without it, `search("app")` would return `True` after inserting only `"apple"` because the path exists. The `is_end` flag distinguishes "this path is a prefix of some word" from "this path IS a word."
2. **Not extracting `_find_node` helper**: Writing separate traversal logic for `search` and `startsWith` leads to code duplication and potential inconsistencies. Factor out the common traversal.
3. **Using a list of size 26 instead of a dictionary**: While valid (and slightly faster for lookups), it uses more memory and requires index arithmetic (`ord(char) - ord('a')`). Dictionaries are more Pythonic and memory-efficient for sparse children.
4. **Returning the wrong thing from search vs startsWith**: `search` checks `is_end`; `startsWith` does not. Mixing these up is a subtle but critical bug.

## Interview Tips

- **How to communicate**: Start by explaining what a trie is: "A tree where each path from root represents a prefix, and nodes store child character mappings." Draw a small trie for "apple" and "app" to show shared prefixes and the `is_end` flag.
- **Clarifying questions to ask**: "Are all characters lowercase English letters?" (Yes). "What is the maximum word length?" (2000). "Are duplicate inserts possible?" (Yes, they are idempotent).
- **Optimization path**: Start with the hash set baseline to show you understand the problem, then explain why a trie is better for prefix queries. Optionally discuss array-based vs dictionary-based nodes for a performance-vs-memory trade-off.
- **Follow-up variations**: "Support wildcard search with '.'" (LeetCode #211 -- DFS at wildcard nodes). "Find all words with a given prefix" (collect all words in the subtree). "Delete a word from the trie" (decrement counts or prune nodes with no children).

## Mnemonics / Memory Aids

- **"Trie = Tree of tries"**: Each path "tries" to spell a word character by character.
- **"Dictionary of children, boolean for end"**: Each node has exactly two fields: `children` (dict) and `is_end` (bool).
- **"search = find + is_end, startsWith = find only"**: The only difference between the two operations is the final `is_end` check.
- **"Insert creates, search follows"**: Insert creates missing nodes; search just follows existing paths and returns None/False on missing characters.

## Self-Assessment Checklist
- [ ] Can I identify this problem type within 2 minutes?
- [ ] Can I code the optimal solution without looking at notes?
- [ ] Can I explain the time/space complexity clearly?
- [ ] Can I handle all edge cases?
- [ ] Can I discuss trade-offs between approaches?

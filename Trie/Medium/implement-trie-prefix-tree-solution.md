# Implement Trie (Prefix Tree)

## Problem Information
- **Difficulty**: Medium
- **Category**: Trie
- **Tags**: Hash Table, String, Design, Trie
- **LeetCode Link**: https://leetcode.com/problems/implement-trie-prefix-tree/

## Problem Statement

A **trie** (pronounced as "try") or **prefix tree** is a tree data structure used to efficiently store and retrieve keys in a dataset of strings. There are various applications of this data structure, such as autocomplete and spellchecker.

Implement the Trie class:
- `Trie()` Initializes the trie object.
- `void insert(String word)` Inserts the string `word` into the trie.
- `boolean search(String word)` Returns `true` if the string `word` is in the trie (i.e., was inserted before), and `false` otherwise.
- `boolean startsWith(String prefix)` Returns `true` if there is a previously inserted string `word` that has the prefix `prefix`, and `false` otherwise.

### Examples

**Example 1:**
```
Input:
["Trie", "insert", "search", "search", "startsWith", "insert", "search"]
[[], ["apple"], ["apple"], ["app"], ["app"], ["app"], ["app"]]
Output: [null, null, true, false, true, null, true]

Explanation:
Trie trie = new Trie();
trie.insert("apple");   // Inserts "apple"
trie.search("apple");   // Returns true
trie.search("app");     // Returns false ("app" was not inserted)
trie.startsWith("app"); // Returns true ("apple" has prefix "app")
trie.insert("app");     // Inserts "app"
trie.search("app");     // Returns true ("app" is now in the trie)
```

### Constraints
- `1 <= word.length, prefix.length <= 2000`
- `word` and `prefix` consist only of lowercase English letters.
- At most `3 * 10^4` calls in total will be made to `insert`, `search`, and `startsWith`.

## Core Concept / Pattern

A trie is a tree where each node represents a character, and paths from root to nodes represent prefixes of stored words. Each node has up to 26 children (for lowercase English letters) and a boolean flag indicating whether the path from root to this node forms a complete word.

The trie excels at prefix-based operations. Unlike a hash set (which requires O(n) for prefix checking over all stored words), a trie checks any prefix in O(m) time where m is the prefix length. Insertion and exact search are also O(m), matching hash set performance for individual words but offering superior prefix query capabilities.

## Approach 1: Brute Force (Hash Set)

### Intuition

Store all words in a set. `search` checks membership directly. `startsWith` iterates through all stored words to check if any starts with the given prefix.

### Algorithm

1. `insert(word)`: Add word to the set.
2. `search(word)`: Check if word is in the set.
3. `startsWith(prefix)`: Iterate through all words and check if any starts with the prefix.

### Implementation

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

### Complexity Analysis
- **Time Complexity**: O(1) average for `insert` and `search` (hash set). O(n * m) for `startsWith` where n is the number of stored words and m is the average word length.
- **Space Complexity**: O(n * m) -- Storing all words.

### Why We Can Do Better

The `startsWith` operation is O(n * m) because it must scan all stored words. A trie data structure handles prefix queries in O(m) time, where m is the length of the prefix, regardless of how many words are stored.

## Approach 2: Optimal Solution (Trie with Dictionary Nodes)

### Intuition

Build a tree where each edge represents a character. To insert "apple", create nodes for a -> p -> p -> l -> e, marking 'e' as a word end. To search, follow the path character by character. `startsWith` is the same as `search` but does not require the end-of-word flag.

### Algorithm

1. Each node is a dictionary of children plus an `is_end` flag.
2. `insert(word)`: Starting from the root, for each character, create a child node if it does not exist, then move to it. Mark the final node as `is_end = True`.
3. `search(word)`: Starting from the root, follow the path for each character. If any character is missing, return `False`. If the path exists and the final node has `is_end = True`, return `True`.
4. `startsWith(prefix)`: Same as `search` but return `True` as long as the path exists (no `is_end` check).

### Implementation

```python
class TrieNode:
    def __init__(self):
        self.children = {}  # char -> TrieNode
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
        """Traverse the trie following the prefix. Return the node at the end, or None."""
        node = self.root
        for char in prefix:
            if char not in node.children:
                return None
            node = node.children[char]
        return node
```

### Complexity Analysis
- **Time Complexity**: O(m) for `insert`, `search`, and `startsWith`, where m is the length of the word or prefix. Each operation traverses at most m nodes.
- **Space Complexity**: O(n * m) in the worst case, where n is the number of words and m is the average length. In practice, shared prefixes reduce this significantly.

## Edge Cases

| Edge Case | Expected Behavior | Why It Matters |
|-----------|-------------------|----------------|
| Search before any insert | Returns `False` | Empty trie has no words |
| Insert then search same word | Returns `True` | Basic functionality |
| Search for prefix of inserted word | `search` returns `False`, `startsWith` returns `True` | Distinguishes exact match from prefix match |
| Insert "app" then search "apple" | Returns `False` | "apple" was not inserted |
| Insert "apple" and "app" | Both `search("apple")` and `search("app")` return `True` | Shorter word is a prefix of longer word |
| Single character word | Works correctly | Minimum length word |
| Same word inserted twice | No duplicate; still returns `True` | Idempotent insertion |
| Empty prefix for startsWith | Returns `True` | Every word starts with empty string (edge case per constraints: prefix length >= 1) |

## Related Problems

| Problem | Relationship | Difficulty |
|---------|-------------|------------|
| Design Add and Search Words Data Structure (LeetCode #211) | Trie with wildcard '.' character support | Medium |
| Word Search II (LeetCode #212) | Trie-based DFS on a board for multiple words | Hard |
| Replace Words (LeetCode #648) | Use trie to find shortest prefix root | Medium |
| Map Sum Pairs (LeetCode #677) | Trie with values summed by prefix | Medium |
| Longest Word in Dictionary (LeetCode #720) | Trie to find longest word buildable one character at a time | Medium |

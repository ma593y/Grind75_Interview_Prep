# Serialize and Deserialize Binary Tree

## Problem Information
- **Difficulty**: Hard
- **Category**: Binary-Tree
- **Tags**: Tree, DFS, BFS, Binary Tree, String, Design
- **LeetCode Link**: https://leetcode.com/problems/serialize-and-deserialize-binary-tree/

## Problem Statement
Serialization is the process of converting a data structure or object into a sequence of bits so that it can be stored in a file or memory buffer, or transmitted across a network connection link to be reconstructed later in the same or another computer environment.

Design an algorithm to serialize and deserialize a binary tree. There is no restriction on how your serialization/deserialization algorithm should work. You just need to ensure that a binary tree can be serialized to a string and this string can be deserialized to the original tree structure.

### Examples
**Example 1:**
```
Input: root = [1,2,3,null,null,4,5]
Output: [1,2,3,null,null,4,5]

        1
       / \
      2   3
         / \
        4   5
```

**Example 2:**
```
Input: root = []
Output: []
```

### Constraints
- The number of nodes in the tree is in the range `[0, 10^4]`.
- `-1000 <= Node.val <= 1000`

## Core Concept / Pattern
To uniquely represent a binary tree as a string, we must encode both the node values and the tree structure (including null children). A preorder DFS traversal with explicit null markers is the most natural approach. During serialization, we output each node's value followed by markers for null children. During deserialization, we consume tokens in the same preorder sequence, using null markers to know when to stop recursing.

## Approach 1: Brute Force (BFS / Level-Order)
### Intuition
Serialize the tree level by level using BFS. Include null markers for missing children. This produces a format similar to LeetCode's own tree representation.

### Algorithm
1. **Serialize**: BFS through the tree. For each node, append its value. For null nodes, append "null". Join with commas.
2. **Deserialize**: Split the string by commas. Use a queue to reconstruct the tree level by level, assigning left and right children from the token stream.

### Implementation
```python
from collections import deque
from typing import Optional

class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right

class Codec:
    def serialize(self, root: Optional[TreeNode]) -> str:
        if not root:
            return ""

        result = []
        queue = deque([root])

        while queue:
            node = queue.popleft()
            if node:
                result.append(str(node.val))
                queue.append(node.left)
                queue.append(node.right)
            else:
                result.append("null")

        return ",".join(result)

    def deserialize(self, data: str) -> Optional[TreeNode]:
        if not data:
            return None

        tokens = data.split(",")
        root = TreeNode(int(tokens[0]))
        queue = deque([root])
        i = 1

        while queue and i < len(tokens):
            node = queue.popleft()

            if tokens[i] != "null":
                node.left = TreeNode(int(tokens[i]))
                queue.append(node.left)
            i += 1

            if i < len(tokens) and tokens[i] != "null":
                node.right = TreeNode(int(tokens[i]))
                queue.append(node.right)
            i += 1

        return root
```

### Complexity Analysis
- **Time Complexity**: O(n) for both serialize and deserialize. Each node is processed once.
- **Space Complexity**: O(n) for the string/tokens and the queue.

### Why We Can Do Better
BFS works well but the deserialization logic is slightly more complex with index management. DFS with preorder traversal produces cleaner, more elegant recursive code.

## Approach 2: Optimal Solution (DFS Preorder with Null Markers)
### Intuition
Use preorder traversal (root, left, right). When we encounter a null node, we output a sentinel marker (e.g., "#"). During deserialization, we consume tokens in the same preorder sequence. When we see "#", we return null. Otherwise, we create a node and recursively build its left and right children.

### Algorithm
1. **Serialize**: Preorder DFS. Append each node's value. Append "#" for null nodes. Separate with commas.
2. **Deserialize**: Split string into tokens. Use an iterator. For each token: if "#", return None. Otherwise, create a node, recurse for left child, recurse for right child.

### Implementation
```python
from typing import Optional

class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right

class Codec:
    def serialize(self, root: Optional[TreeNode]) -> str:
        result = []

        def dfs(node: Optional[TreeNode]) -> None:
            if not node:
                result.append("#")
                return
            result.append(str(node.val))
            dfs(node.left)
            dfs(node.right)

        dfs(root)
        return ",".join(result)

    def deserialize(self, data: str) -> Optional[TreeNode]:
        tokens = iter(data.split(","))

        def dfs() -> Optional[TreeNode]:
            val = next(tokens)
            if val == "#":
                return None
            node = TreeNode(int(val))
            node.left = dfs()
            node.right = dfs()
            return node

        return dfs()
```

### Complexity Analysis
- **Time Complexity**: O(n) for both operations. Each node is visited exactly once.
- **Space Complexity**: O(n) for the serialized string. O(h) for the recursion stack during DFS, where h is the tree height (worst case O(n) for a skewed tree).

## Edge Cases
| Edge Case | Expected Behavior | Why It Matters |
|-----------|-------------------|----------------|
| Empty tree (root = None) | Serialize to "#", deserialize back to None | Must handle gracefully |
| Single node | "val,#,#" format | Null children must be explicit |
| Left-skewed tree | Long chain of val,val,...,#,#,...,#,# | Tests recursion depth |
| Right-skewed tree | Alternating val,#,val,#,...,#,# | Opposite skew pattern |
| Negative values | "-1" serialized correctly | Values can be negative |
| Large tree (10^4 nodes) | Must not exceed recursion limit | Consider iterative for very deep trees |

## Related Problems
| Problem | Relationship | Difficulty |
|---------|-------------|------------|
| Serialize and Deserialize BST (#449) | BST variant (can omit nulls due to BST property) | Medium |
| Construct Binary Tree from Preorder and Inorder (#105) | Tree reconstruction from traversals | Medium |
| Verify Preorder Serialization (#331) | Verify serialization without building tree | Medium |
| Encode and Decode Strings (#271) | General serialization problem | Medium |

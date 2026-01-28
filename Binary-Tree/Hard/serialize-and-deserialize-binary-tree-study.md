# Serialize and Deserialize Binary Tree - Study Guide

## Problem Summary
- **Difficulty**: Hard
- **Category**: Binary-Tree
- **Tags**: Tree, DFS, BFS, Binary Tree, String, Design
- **LeetCode Link**: https://leetcode.com/problems/serialize-and-deserialize-binary-tree/

> **One-liner**: Design encode/decode functions to convert a binary tree to a string and back, using preorder DFS with null markers to preserve the exact structure.

## Key Intuition
A binary tree can be uniquely represented by recording its preorder traversal with explicit null markers. The null markers tell us exactly where subtrees end, so during deserialization we can reconstruct the tree by consuming tokens in the same preorder sequence. When we see a null marker, we know there is no child; otherwise, we create a node and recurse.

## Approaches Overview
| Approach | Time | Space | Trade-off |
|----------|------|-------|-----------|
| BFS (Level-Order) | O(n) | O(n) | Matches LeetCode format, slightly more complex deserialization |
| DFS Preorder with Null Markers | O(n) | O(n) | Elegant recursive code, natural serialize/deserialize symmetry |

## Approach 1: BFS (Level-Order)
### Core Idea
Serialize level by level. For each node, output its value; for null nodes, output "null". Deserialize by creating nodes level by level using a queue.

### Code
```python
from collections import deque

class Codec:
    def serialize(self, root):
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

    def deserialize(self, data):
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

### Walkthrough
Tree: `[1, 2, 3, null, null, 4, 5]`
- Serialize BFS: "1,2,3,null,null,4,5,null,null,null,null"
- Deserialize: token 0=1 (root), token 1=2 (left of 1), token 2=3 (right of 1), tokens 3,4=null,null (children of 2), tokens 5,6=4,5 (children of 3)

## Approach 2: DFS Preorder with Null Markers (Optimal)
### Core Idea
Serialize using preorder DFS. Record each value and use "#" for null nodes. Deserialize by iterating tokens in the same preorder order, recursively building left then right children.

### Code
```python
class Codec:
    def serialize(self, root):
        result = []
        def dfs(node):
            if not node:
                result.append("#")
                return
            result.append(str(node.val))
            dfs(node.left)
            dfs(node.right)
        dfs(root)
        return ",".join(result)

    def deserialize(self, data):
        tokens = iter(data.split(","))
        def dfs():
            val = next(tokens)
            if val == "#":
                return None
            node = TreeNode(int(val))
            node.left = dfs()
            node.right = dfs()
            return node
        return dfs()
```

### Walkthrough
Tree: `[1, 2, 3, null, null, 4, 5]`
```
        1
       / \
      2   3
         / \
        4   5
```
- **Serialize**: dfs(1) -> "1", dfs(2) -> "2", dfs(None) -> "#", dfs(None) -> "#", dfs(3) -> "3", dfs(4) -> "4", dfs(None) -> "#", dfs(None) -> "#", dfs(5) -> "5", dfs(None) -> "#", dfs(None) -> "#"
- Result: "1,2,#,#,3,4,#,#,5,#,#"
- **Deserialize**: next()=1 (root), left=dfs() -> next()=2 -> left=dfs()->#->None, right=dfs()->#->None -> node(2). right=dfs() -> next()=3 -> left=dfs()->4->... -> node(3). Rebuilds exact tree.

## Complexity Analysis
- Both approaches: **O(n) time** for serialize and deserialize, visiting each node once.
- BFS: O(n) space for the queue and the output string.
- DFS: O(n) space for the output string. O(h) for the recursion stack (worst case O(n) for a skewed tree).

## Common Mistakes
1. **Forgetting null markers**: Without explicit null markers, the tree structure is ambiguous. Two different trees could have the same preorder sequence.
2. **Using a list index instead of an iterator for deserialization**: An iterator (`iter()`) naturally tracks the current position across recursive calls. A list index requires passing by reference (e.g., `self.idx`).
3. **Not handling the empty tree**: An empty tree should serialize to a single "#" (DFS) or "" (BFS) and deserialize back to None.
4. **Delimiter issues**: If using a single character like "," as a delimiter, make sure multi-digit and negative numbers are handled correctly.

## Interview Tips
- **How to communicate**: "I will use preorder DFS with null markers. Serialize records each value and '#' for nulls. Deserialize consumes tokens in the same order, using '#' to terminate recursion."
- **Clarifying questions**: "Can node values be negative?" (Yes.) "Is there a limit on tree size?" "What format should the string be?"
- **Why preorder DFS is elegant**: The serialize and deserialize are symmetric -- both follow the exact same traversal order (root, left, right).
- **Follow-up**: "Could you do this for a BST more efficiently?" (Yes -- BST order property means you do not need null markers; just preorder values suffice since inorder is implicitly sorted.)

## Mnemonics / Memory Aids
- **"Preorder + Nulls = Unique Tree"**: Preorder traversal with explicit null markers uniquely identifies any binary tree.
- **"Serialize DFS out, Deserialize DFS in"**: Both operations follow the same preorder path.
- **"# means stop, number means go"**: During deserialization, "#" returns None, a number creates a node and recurses.

## Self-Assessment Checklist
- [ ] Can I explain why null markers are necessary for unique tree reconstruction?
- [ ] Can I write both serialize and deserialize from memory?
- [ ] Can I trace through an example showing token consumption during deserialization?
- [ ] Can I handle edge cases (empty tree, single node, negative values)?
- [ ] Can I explain the difference between this problem and the BST variant (#449)?

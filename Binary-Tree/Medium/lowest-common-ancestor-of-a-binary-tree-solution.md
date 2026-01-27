# Lowest Common Ancestor of a Binary Tree

## Problem Information
- **Difficulty**: Medium
- **Category**: Binary-Tree
- **Tags**: Tree, DFS, Recursion, Binary Tree
- **LeetCode Link**: https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/

## Problem Statement
Given a binary tree, find the lowest common ancestor (LCA) of two given nodes in the tree.

According to the definition of LCA: "The lowest common ancestor is defined between two nodes `p` and `q` as the lowest node in `T` that has both `p` and `q` as descendants (where we allow a node to be a descendant of itself)."

### Examples
**Example 1:**
```
Input: root = [3,5,1,6,2,0,8,null,null,7,4], p = 5, q = 1
Output: 3

         3
        / \
       5   1
      / \ / \
     6  2 0  8
       / \
      7   4

Explanation: The LCA of nodes 5 and 1 is 3.
```

**Example 2:**
```
Input: root = [3,5,1,6,2,0,8,null,null,7,4], p = 5, q = 4
Output: 5

Explanation: The LCA of nodes 5 and 4 is 5, since a node can be a descendant of itself.
```

**Example 3:**
```
Input: root = [1,2], p = 1, q = 2
Output: 1
```

### Constraints
- The number of nodes in the tree is in the range `[2, 10^5]`.
- `-10^9 <= Node.val <= 10^9`
- All `Node.val` are **unique**.
- `p != q`
- `p` and `q` will exist in the tree.

## Core Concept / Pattern
This is a general binary tree (not a BST), so we cannot use value comparisons to guide the search. Instead, we use a recursive DFS approach: if the current node is `p` or `q`, it could be the LCA. Otherwise, search both subtrees. If both subtrees return non-null results, the current node is the LCA. If only one returns non-null, propagate that result upward.

## Approach 1: Path Finding (Brute Force)
### Intuition
Find the path from root to `p` and from root to `q`. The last common node in both paths is the LCA.

### Algorithm
1. Find the path from root to `p` using DFS.
2. Find the path from root to `q` using DFS.
3. Compare the two paths and return the last common node.

### Implementation
```python
from typing import Optional, List

class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right

class Solution:
    def lowestCommonAncestor(self, root: TreeNode, p: TreeNode, q: TreeNode) -> TreeNode:
        def find_path(node: TreeNode, target: TreeNode, path: List[TreeNode]) -> bool:
            if not node:
                return False
            path.append(node)
            if node == target:
                return True
            if find_path(node.left, target, path) or find_path(node.right, target, path):
                return True
            path.pop()
            return False

        path_p, path_q = [], []
        find_path(root, p, path_p)
        find_path(root, q, path_q)

        lca = root
        for i in range(min(len(path_p), len(path_q))):
            if path_p[i] == path_q[i]:
                lca = path_p[i]
            else:
                break

        return lca
```

### Complexity Analysis
- **Time Complexity**: O(n) for each path search, so O(n) total.
- **Space Complexity**: O(n) for storing paths and recursion stack.

### Why We Can Do Better
While time complexity is the same, we can solve this in a single DFS pass without explicitly storing paths, using less space and simpler code.

## Approach 2: Recursive DFS (Optimal)
### Intuition
At each node, recursively search the left and right subtrees for `p` and `q`. There are three cases:
- If both subtrees return non-null, the current node is the LCA (p and q are on different sides).
- If only one subtree returns non-null, propagate that result up (both p and q are on the same side).
- If the current node is `p` or `q`, return it immediately (it might be the LCA itself).

### Algorithm
1. Base case: if node is `None`, return `None`.
2. If node equals `p` or `q`, return node.
3. Recursively search left subtree and right subtree.
4. If both return non-null, current node is the LCA.
5. Otherwise, return whichever is non-null (or None if both are null).

### Implementation
```python
from typing import Optional

class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right

class Solution:
    def lowestCommonAncestor(self, root: TreeNode, p: TreeNode, q: TreeNode) -> TreeNode:
        if not root or root == p or root == q:
            return root

        left = self.lowestCommonAncestor(root.left, p, q)
        right = self.lowestCommonAncestor(root.right, p, q)

        if left and right:
            return root

        return left if left else right
```

### Complexity Analysis
- **Time Complexity**: O(n) where n is the number of nodes. In the worst case, we visit every node.
- **Space Complexity**: O(h) where h is the height of the tree, for the recursion stack.

## Edge Cases
| Edge Case | Expected Behavior | Why It Matters |
|-----------|-------------------|----------------|
| One node is ancestor of the other | Return the ancestor node | A node is a descendant of itself |
| p and q are siblings | Return their parent | Simple case |
| p and q are in different subtrees | Return their common ancestor | Main case |
| Root is p or q | Return root | Root can be the LCA |
| Deep tree (skewed) | Still works, O(n) stack | Test recursion depth |

## Related Problems
| Problem | Relationship | Difficulty |
|---------|-------------|------------|
| Lowest Common Ancestor of a BST (#235) | Simpler version using BST property | Medium |
| LCA of Binary Tree III (#1650) | Nodes have parent pointers | Medium |
| LCA of Binary Tree IV (#1676) | Find LCA of multiple nodes | Medium |
| LCA of Deepest Leaves (#1123) | Find LCA of deepest nodes | Medium |

# Lowest Common Ancestor of a BST

## Problem Information
- **Difficulty**: Easy
- **Category**: Binary-Tree
- **Tags**: Tree, BST, DFS, Binary Search Tree
- **LeetCode Link**: https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-search-tree/

## Problem Statement
Given a binary search tree (BST), find the lowest common ancestor (LCA) node of two given nodes in the BST.

According to the definition of LCA on Wikipedia: "The lowest common ancestor is defined between two nodes `p` and `q` as the lowest node in `T` that has both `p` and `q` as descendants (where we allow a node to be a descendant of itself)."

### Examples
**Example 1:**
```
Input: root = [6,2,8,0,4,7,9,null,null,3,5], p = 2, q = 8
Output: 6
Explanation: The LCA of nodes 2 and 8 is 6.

        6
      /   \
     2     8
    / \   / \
   0   4 7   9
      / \
     3   5
```

**Example 2:**
```
Input: root = [6,2,8,0,4,7,9,null,null,3,5], p = 2, q = 4
Output: 2
Explanation: The LCA of nodes 2 and 4 is 2, since a node can be a descendant of itself.
```

**Example 3:**
```
Input: root = [2,1], p = 2, q = 1
Output: 2
```

### Constraints
- The number of nodes in the tree is in the range `[2, 10^5]`.
- `-10^9 <= Node.val <= 10^9`
- All `Node.val` are unique.
- `p != q`
- `p` and `q` will exist in the BST.

## Core Concept / Pattern
This problem leverages the **BST property**: for any node, all values in the left subtree are less than the node's value, and all values in the right subtree are greater. This property allows us to determine which direction to search without exploring both subtrees.

The LCA is the first node where p and q diverge to different subtrees (one goes left, one goes right), or the first node that equals p or q. This is the "split point" in the BST.

## Approach 1: Brute Force (General LCA without BST property)
### Intuition
Ignore the BST property and find paths from root to both p and q. The last common node in both paths is the LCA.

### Algorithm
1. Find the path from root to p.
2. Find the path from root to q.
3. Compare paths and return the last common node.

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
        def findPath(node: TreeNode, target: TreeNode, path: List[TreeNode]) -> bool:
            if not node:
                return False
            path.append(node)
            if node == target:
                return True
            if findPath(node.left, target, path) or findPath(node.right, target, path):
                return True
            path.pop()
            return False

        path_p, path_q = [], []
        findPath(root, p, path_p)
        findPath(root, q, path_q)

        lca = root
        for i in range(min(len(path_p), len(path_q))):
            if path_p[i] == path_q[i]:
                lca = path_p[i]
            else:
                break
        return lca
```

### Complexity Analysis
- **Time Complexity**: O(n) where n is the number of nodes. We may traverse the entire tree to find paths.
- **Space Complexity**: O(n) for storing paths.

### Why We Can Do Better
We are not exploiting the BST property at all. In a BST, we can determine the direction to search at each node by comparing values, which gives us a direct path to the LCA without backtracking or storing paths.

## Approach 2: Optimal Solution (Exploit BST Property)
### Intuition
Starting from the root, if both p and q are smaller than the current node, the LCA must be in the left subtree. If both are larger, the LCA is in the right subtree. Otherwise, the current node is the split point and therefore the LCA.

### Algorithm
1. Start at the root.
2. If both p and q values are less than current node's value, move to the left child.
3. If both p and q values are greater than current node's value, move to the right child.
4. Otherwise, the current node is the LCA (the split point).

### Implementation
```python
class Solution:
    def lowestCommonAncestor(self, root: TreeNode, p: TreeNode, q: TreeNode) -> TreeNode:
        current = root
        while current:
            if p.val < current.val and q.val < current.val:
                current = current.left
            elif p.val > current.val and q.val > current.val:
                current = current.right
            else:
                return current
```

### Complexity Analysis
- **Time Complexity**: O(h) where h is the height of the BST. For a balanced BST, this is O(log n). For a skewed BST, this is O(n).
- **Space Complexity**: O(1) since we use iterative traversal with no extra space.

## Edge Cases
| Edge Case | Expected Behavior | Why It Matters |
|-----------|-------------------|----------------|
| p is ancestor of q | Return p | A node is its own descendant |
| q is ancestor of p | Return q | Symmetric case |
| p and q are adjacent | Return the parent | Direct parent-child relationship |
| p and q are in different subtrees | Return the split point | Standard LCA case |
| Tree has only 2 nodes | Return root | Minimal tree case |

## Related Problems
| Problem | Relationship | Difficulty |
|---------|-------------|------------|
| Lowest Common Ancestor of a Binary Tree (#236) | Same problem without BST property | Medium |
| Binary Search Tree Iterator (#173) | BST traversal pattern | Medium |
| Validate Binary Search Tree (#98) | Uses BST property | Medium |

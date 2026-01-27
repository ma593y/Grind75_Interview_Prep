# Diameter of Binary Tree

## Problem Information
- **Difficulty**: Easy
- **Category**: Binary-Tree
- **Tags**: Tree, DFS, Recursion, Binary Tree
- **LeetCode Link**: https://leetcode.com/problems/diameter-of-binary-tree/

## Problem Statement
Given the `root` of a binary tree, return the length of the **diameter** of the tree.

The **diameter** of a binary tree is the **length of the longest path** between any two nodes in a tree. This path may or may not pass through the `root`.

The **length** of a path between two nodes is represented by the number of **edges** between them.

### Examples
**Example 1:**
```
Input: root = [1,2,3,4,5]
Output: 3

        1
       / \
      2   3
     / \
    4   5

The longest path is [4,2,1,3] or [5,2,1,3], which has length 3.
```

**Example 2:**
```
Input: root = [1,2]
Output: 1
```

### Constraints
- The number of nodes in the tree is in the range `[1, 10^4]`.
- `-100 <= Node.val <= 100`

## Core Concept / Pattern
The diameter through any node equals the sum of the heights of its left and right subtrees. The overall diameter is the maximum diameter across all nodes. This is a classic bottom-up DFS pattern where we compute heights while tracking a global maximum.

The key insight is that the longest path passing through a node is `left_height + right_height`, and we need to find the maximum of this value across all nodes in the tree.

## Approach 1: Brute Force
### Intuition
For each node, compute the height of its left and right subtrees separately, then calculate the diameter through that node. Track the maximum diameter found.

### Algorithm
1. Write a helper to compute the height of a subtree.
2. For each node, compute `height(left) + height(right)` as the diameter through that node.
3. Recursively check every node and track the maximum diameter.

### Implementation
```python
from typing import Optional

class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right

class Solution:
    def diameterOfBinaryTree(self, root: Optional[TreeNode]) -> int:
        def height(node: Optional[TreeNode]) -> int:
            if not node:
                return 0
            return 1 + max(height(node.left), height(node.right))

        def diameter(node: Optional[TreeNode]) -> int:
            if not node:
                return 0
            # Diameter through this node
            through_node = height(node.left) + height(node.right)
            # Diameter in left or right subtree (not passing through this node)
            left_dia = diameter(node.left)
            right_dia = diameter(node.right)
            return max(through_node, left_dia, right_dia)

        return diameter(root)
```

### Complexity Analysis
- **Time Complexity**: O(n^2) in the worst case (skewed tree). For each node, we compute heights which takes O(n).
- **Space Complexity**: O(n) for the recursion stack in the worst case.

### Why We Can Do Better
Height is recomputed for overlapping subtrees. We can combine height calculation and diameter tracking in a single DFS pass.

## Approach 2: Bottom-Up DFS (Optimal)
### Intuition
Compute height bottom-up. At each node, the diameter through that node is `left_height + right_height`. Use an instance variable or closure to track the global maximum diameter during the traversal.

### Algorithm
1. Initialize a variable `max_diameter = 0`.
2. Define a DFS helper that returns the height of a subtree.
3. At each node, compute left and right heights via recursion.
4. Update `max_diameter = max(max_diameter, left_height + right_height)`.
5. Return `1 + max(left_height, right_height)` as the height.
6. After DFS completes, return `max_diameter`.

### Implementation
```python
from typing import Optional

class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right

class Solution:
    def diameterOfBinaryTree(self, root: Optional[TreeNode]) -> int:
        self.max_diameter = 0

        def dfs(node: Optional[TreeNode]) -> int:
            if not node:
                return 0

            left_height = dfs(node.left)
            right_height = dfs(node.right)

            # Update diameter: path through this node
            self.max_diameter = max(self.max_diameter, left_height + right_height)

            # Return height of this subtree
            return 1 + max(left_height, right_height)

        dfs(root)
        return self.max_diameter
```

### Complexity Analysis
- **Time Complexity**: O(n) where n is the number of nodes. Each node is visited exactly once.
- **Space Complexity**: O(h) where h is the height of the tree, for the recursion stack. O(n) in the worst case (skewed), O(log n) for balanced.

## Edge Cases
| Edge Case | Expected Behavior | Why It Matters |
|-----------|-------------------|----------------|
| Single node | Return 0 | No edges in a single-node tree |
| Two nodes | Return 1 | One edge between root and child |
| Left-skewed tree | Return n-1 | Diameter is the entire path |
| Diameter not through root | Return correct value | Must check all nodes, not just root |
| Perfect binary tree | Return 2 * height | Path goes through root |

## Related Problems
| Problem | Relationship | Difficulty |
|---------|-------------|------------|
| Maximum Depth of Binary Tree (#104) | Height computation is a sub-problem | Easy |
| Balanced Binary Tree (#110) | Similar bottom-up height pattern | Easy |
| Binary Tree Maximum Path Sum (#124) | Same pattern with values instead of edges | Hard |
| Longest Univalue Path (#687) | Similar diameter concept with value constraint | Medium |

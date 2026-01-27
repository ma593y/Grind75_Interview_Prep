# Balanced Binary Tree

## Problem Information
- **Difficulty**: Easy
- **Category**: Binary-Tree
- **Tags**: Tree, DFS, Recursion, Binary Tree
- **LeetCode Link**: https://leetcode.com/problems/balanced-binary-tree/

## Problem Statement
Given a binary tree, determine if it is **height-balanced**.

A height-balanced binary tree is a binary tree in which the depth of the two subtrees of every node never differs by more than one.

### Examples
**Example 1:**
```
Input: root = [3,9,20,null,null,15,7]
Output: true

      3
     / \
    9  20
      /  \
     15   7
```

**Example 2:**
```
Input: root = [1,2,2,3,3,null,null,4,4]
Output: false

         1
        / \
       2   2
      / \
     3   3
    / \
   4   4
```

**Example 3:**
```
Input: root = []
Output: true
```

### Constraints
- The number of nodes in the tree is in the range `[0, 5000]`.
- `-10^4 <= Node.val <= 10^4`

## Core Concept / Pattern
This problem tests the ability to compute tree heights recursively while checking a balance condition at every node. The naive approach computes height separately for each node, leading to redundant work. The optimal approach combines height computation and balance checking in a single pass using a bottom-up DFS.

The key insight is that you can propagate a "sentinel" value (like -1) up the recursion to indicate an imbalanced subtree, avoiding the need for a separate balance check.

## Approach 1: Top-Down (Brute Force)
### Intuition
For each node, compute the height of its left and right subtrees. If the difference exceeds 1, the tree is not balanced. Repeat for every node. This approach re-computes heights many times.

### Algorithm
1. Write a helper function to compute the height of a tree.
2. For the current node, check if `|height(left) - height(right)| <= 1`.
3. Recursively check if both left and right subtrees are also balanced.
4. Return true only if all conditions hold.

### Implementation
```python
from typing import Optional

class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right

class Solution:
    def isBalanced(self, root: Optional[TreeNode]) -> bool:
        def height(node: Optional[TreeNode]) -> int:
            if not node:
                return 0
            return 1 + max(height(node.left), height(node.right))

        if not root:
            return True

        left_height = height(root.left)
        right_height = height(root.right)

        return (abs(left_height - right_height) <= 1
                and self.isBalanced(root.left)
                and self.isBalanced(root.right))
```

### Complexity Analysis
- **Time Complexity**: O(n log n) for a balanced tree (each level computes heights for nodes below), O(n^2) for a skewed tree.
- **Space Complexity**: O(n) due to recursion stack in the worst case.

### Why We Can Do Better
The brute force approach computes the height of the same subtrees repeatedly. We can combine the height calculation and balance check into a single bottom-up traversal.

## Approach 2: Bottom-Up DFS (Optimal)
### Intuition
Compute the height bottom-up. If at any point a subtree is found to be unbalanced, return -1 as a sentinel. This way, we check balance and compute height in a single pass.

### Algorithm
1. Define a helper that returns the height of a subtree, or -1 if unbalanced.
2. Recursively get the height of left and right subtrees.
3. If either returns -1, or the height difference exceeds 1, return -1.
4. Otherwise, return the actual height.
5. The tree is balanced if the final result is not -1.

### Implementation
```python
from typing import Optional

class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right

class Solution:
    def isBalanced(self, root: Optional[TreeNode]) -> bool:
        def check(node: Optional[TreeNode]) -> int:
            if not node:
                return 0

            left = check(node.left)
            if left == -1:
                return -1

            right = check(node.right)
            if right == -1:
                return -1

            if abs(left - right) > 1:
                return -1

            return 1 + max(left, right)

        return check(root) != -1
```

### Complexity Analysis
- **Time Complexity**: O(n) where n is the number of nodes. Each node is visited exactly once.
- **Space Complexity**: O(h) where h is the height of the tree, for the recursion stack.

## Edge Cases
| Edge Case | Expected Behavior | Why It Matters |
|-----------|-------------------|----------------|
| Empty tree | Return True | An empty tree is balanced by definition |
| Single node | Return True | No subtrees to compare |
| Left-skewed tree (linked list) | Return False (if depth > 1) | Tests imbalance detection |
| Perfect binary tree | Return True | Ideal balanced tree |
| Locally balanced but globally unbalanced | Return False | Must check every node |

## Related Problems
| Problem | Relationship | Difficulty |
|---------|-------------|------------|
| Maximum Depth of Binary Tree (#104) | Height computation is a sub-problem | Easy |
| Diameter of Binary Tree (#543) | Similar bottom-up height pattern | Easy |
| Binary Tree Maximum Path Sum (#124) | Bottom-up computation with early termination | Hard |

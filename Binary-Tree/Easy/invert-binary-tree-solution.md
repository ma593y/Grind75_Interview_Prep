# Invert Binary Tree

## Problem Information
- **Difficulty**: Easy
- **Category**: Binary-Tree
- **Tags**: Tree, DFS, BFS, Recursion, Binary Tree
- **LeetCode Link**: https://leetcode.com/problems/invert-binary-tree/

## Problem Statement
Given the `root` of a binary tree, invert the tree, and return its root.

Inverting a binary tree means swapping the left and right children of every node in the tree.

### Examples
**Example 1:**
```
Input: root = [4,2,7,1,3,6,9]
Output: [4,7,2,9,6,3,1]

Original:        Inverted:
     4                4
   /   \            /   \
  2     7          7     2
 / \   / \        / \   / \
1   3 6   9      9   6 3   1
```

**Example 2:**
```
Input: root = [2,1,3]
Output: [2,3,1]
```

**Example 3:**
```
Input: root = []
Output: []
```

### Constraints
- The number of nodes in the tree is in the range `[0, 100]`.
- `-100 <= Node.val <= 100`

## Core Concept / Pattern
This problem tests your understanding of recursive tree traversal and the ability to modify tree structure in place. The key insight is that inverting a binary tree is equivalent to swapping the left and right subtrees of every node. This can be done recursively (DFS) or iteratively (BFS).

The recursive approach naturally mirrors the problem definition: to invert a tree, swap the children of the root, then recursively invert the left and right subtrees. This is a classic example where recursion provides an elegant and concise solution.

## Approach 1: Recursive DFS
### Intuition
At each node, we simply swap its left and right children, then recursively do the same for the (now swapped) children. The base case is when we reach a null node.

### Algorithm
1. If the root is `None`, return `None`.
2. Swap `root.left` and `root.right`.
3. Recursively invert `root.left`.
4. Recursively invert `root.right`.
5. Return `root`.

### Implementation
```python
from typing import Optional

class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right

class Solution:
    def invertTree(self, root: Optional[TreeNode]) -> Optional[TreeNode]:
        if not root:
            return None

        # Swap left and right children
        root.left, root.right = root.right, root.left

        # Recursively invert subtrees
        self.invertTree(root.left)
        self.invertTree(root.right)

        return root
```

### Complexity Analysis
- **Time Complexity**: O(n) where n is the number of nodes. We visit every node exactly once.
- **Space Complexity**: O(h) where h is the height of the tree, due to the recursion stack. In the worst case (skewed tree), this is O(n). In the best case (balanced tree), this is O(log n).

### Why We Can Do Better
This recursive approach is already optimal in terms of time complexity. However, if the tree is very deep (skewed), the recursion stack could overflow. An iterative BFS approach avoids deep recursion.

## Approach 2: Iterative BFS
### Intuition
Use a queue to perform a level-order traversal. At each node, swap its left and right children, then add the children to the queue for processing.

### Algorithm
1. If root is `None`, return `None`.
2. Initialize a queue with the root.
3. While the queue is not empty:
   - Dequeue a node.
   - Swap its left and right children.
   - Enqueue the non-null children.
4. Return root.

### Implementation
```python
from typing import Optional
from collections import deque

class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right

class Solution:
    def invertTree(self, root: Optional[TreeNode]) -> Optional[TreeNode]:
        if not root:
            return None

        queue = deque([root])

        while queue:
            node = queue.popleft()

            # Swap children
            node.left, node.right = node.right, node.left

            # Add children to queue
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)

        return root
```

### Complexity Analysis
- **Time Complexity**: O(n) where n is the number of nodes. Every node is visited once.
- **Space Complexity**: O(n) in the worst case, where the queue holds all nodes at the widest level of the tree. For a complete binary tree, the last level has ~n/2 nodes.

## Edge Cases
| Edge Case | Expected Behavior | Why It Matters |
|-----------|-------------------|----------------|
| Empty tree (root is None) | Return None | Base case for recursion |
| Single node | Return the node unchanged | No children to swap |
| Left-skewed tree | Becomes right-skewed | Tests one-sided trees |
| Perfect binary tree | Mirrors symmetrically | Standard case |

## Related Problems
| Problem | Relationship | Difficulty |
|---------|-------------|------------|
| Symmetric Tree (#101) | Check if a tree is its own inverse | Easy |
| Binary Tree Level Order Traversal (#102) | Uses similar BFS traversal | Medium |
| Flip Equivalent Binary Trees (#951) | Generalized inversion check | Medium |

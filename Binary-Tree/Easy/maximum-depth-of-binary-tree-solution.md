# Maximum Depth of Binary Tree

## Problem Information
- **Difficulty**: Easy
- **Category**: Binary-Tree
- **Tags**: Tree, DFS, BFS, Recursion, Binary Tree
- **LeetCode Link**: https://leetcode.com/problems/maximum-depth-of-binary-tree/

## Problem Statement
Given the `root` of a binary tree, return its **maximum depth**.

A binary tree's **maximum depth** is the number of nodes along the longest path from the root node down to the farthest leaf node.

### Examples
**Example 1:**
```
Input: root = [3,9,20,null,null,15,7]
Output: 3

      3
     / \
    9  20
      /  \
     15   7
```

**Example 2:**
```
Input: root = [1,null,2]
Output: 2
```

### Constraints
- The number of nodes in the tree is in the range `[0, 10^4]`.
- `-100 <= Node.val <= 100`

## Core Concept / Pattern
This is one of the most fundamental tree problems. The maximum depth of a tree equals `1 + max(depth(left), depth(right))`. This recursive definition directly translates into an elegant DFS solution. Alternatively, BFS level-order traversal counts the number of levels, which equals the maximum depth.

## Approach 1: Recursive DFS
### Intuition
The depth of a tree rooted at a node is 1 (for the current node) plus the maximum depth of its left and right subtrees. The base case is a null node, which has depth 0.

### Algorithm
1. If the root is `None`, return 0.
2. Recursively compute the depth of the left subtree.
3. Recursively compute the depth of the right subtree.
4. Return `1 + max(left_depth, right_depth)`.

### Implementation
```python
from typing import Optional

class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right

class Solution:
    def maxDepth(self, root: Optional[TreeNode]) -> int:
        if not root:
            return 0

        left_depth = self.maxDepth(root.left)
        right_depth = self.maxDepth(root.right)

        return 1 + max(left_depth, right_depth)
```

### Complexity Analysis
- **Time Complexity**: O(n) where n is the number of nodes. Every node is visited exactly once.
- **Space Complexity**: O(h) where h is the height of the tree, for the recursion stack. Worst case O(n) for a skewed tree, best case O(log n) for a balanced tree.

### Why We Can Do Better
The recursive approach is already O(n) time but uses O(h) stack space. An iterative BFS approach uses O(w) space (width of tree) which can be more predictable, and avoids stack overflow for very deep trees.

## Approach 2: Iterative BFS
### Intuition
Perform a level-order traversal using a queue. Each time we finish processing an entire level, increment a depth counter. The final counter value is the maximum depth.

### Algorithm
1. If root is `None`, return 0.
2. Initialize a queue with the root and set `depth = 0`.
3. While the queue is not empty:
   - Get the number of nodes at the current level.
   - Process all nodes at this level, adding their children to the queue.
   - Increment `depth`.
4. Return `depth`.

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
    def maxDepth(self, root: Optional[TreeNode]) -> int:
        if not root:
            return 0

        queue = deque([root])
        depth = 0

        while queue:
            level_size = len(queue)
            for _ in range(level_size):
                node = queue.popleft()
                if node.left:
                    queue.append(node.left)
                if node.right:
                    queue.append(node.right)
            depth += 1

        return depth
```

### Complexity Analysis
- **Time Complexity**: O(n) where n is the number of nodes. Every node is visited exactly once.
- **Space Complexity**: O(w) where w is the maximum width of the tree. For a complete binary tree, the last level has ~n/2 nodes, so worst case O(n).

## Edge Cases
| Edge Case | Expected Behavior | Why It Matters |
|-----------|-------------------|----------------|
| Empty tree (None) | Return 0 | Base case |
| Single node | Return 1 | Minimum non-empty tree |
| Left-skewed tree | Return n | Depth equals number of nodes |
| Right-skewed tree | Return n | Same as left-skewed |
| Perfect binary tree | Return log2(n+1) | Balanced case |

## Related Problems
| Problem | Relationship | Difficulty |
|---------|-------------|------------|
| Minimum Depth of Binary Tree (#111) | Similar but finds shortest path to leaf | Easy |
| Balanced Binary Tree (#110) | Uses depth computation as sub-problem | Easy |
| Maximum Depth of N-ary Tree (#559) | Generalized to N children | Easy |
| Diameter of Binary Tree (#543) | Uses height (related to depth) as sub-problem | Easy |

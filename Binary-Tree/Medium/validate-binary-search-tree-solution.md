# Validate Binary Search Tree

## Problem Information
- **Difficulty**: Medium
- **Category**: Binary-Tree
- **Tags**: Tree, DFS, BST, Binary Search Tree, Recursion
- **LeetCode Link**: https://leetcode.com/problems/validate-binary-search-tree/

## Problem Statement
Given the `root` of a binary tree, determine if it is a valid binary search tree (BST).

A **valid BST** is defined as follows:
- The left subtree of a node contains only nodes with keys **less than** the node's key.
- The right subtree of a node contains only nodes with keys **greater than** the node's key.
- Both the left and right subtrees must also be binary search trees.

### Examples
**Example 1:**
```
Input: root = [2,1,3]
Output: true

    2
   / \
  1   3
```

**Example 2:**
```
Input: root = [5,1,4,null,null,3,6]
Output: false

    5
   / \
  1   4
     / \
    3   6

Explanation: The root node's value is 5 but its right child's value is 4.
```

### Constraints
- The number of nodes in the tree is in the range `[1, 10^4]`.
- `-2^31 <= Node.val <= 2^31 - 1`

## Core Concept / Pattern
The key insight is that simply checking each node against its direct parent is insufficient. Every node in the left subtree must be less than the root, and every node in the right subtree must be greater. We must propagate valid ranges (lower and upper bounds) down the tree.

Alternatively, an inorder traversal of a valid BST produces a strictly increasing sequence. We can validate this property.

## Approach 1: Inorder Traversal
### Intuition
Perform an inorder traversal (left, root, right). In a valid BST, this produces values in strictly ascending order. If any value is not greater than the previous, the tree is not a valid BST.

### Algorithm
1. Perform an inorder traversal.
2. Track the previously visited value.
3. If the current value is less than or equal to the previous value, return False.
4. If the entire traversal completes, return True.

### Implementation
```python
from typing import Optional

class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right

class Solution:
    def isValidBST(self, root: Optional[TreeNode]) -> bool:
        self.prev = float('-inf')

        def inorder(node: Optional[TreeNode]) -> bool:
            if not node:
                return True

            # Check left subtree
            if not inorder(node.left):
                return False

            # Check current node
            if node.val <= self.prev:
                return False
            self.prev = node.val

            # Check right subtree
            return inorder(node.right)

        return inorder(root)
```

### Complexity Analysis
- **Time Complexity**: O(n) where n is the number of nodes.
- **Space Complexity**: O(h) for the recursion stack.

### Why We Can Do Better
This approach is already O(n), but the range-based approach is more flexible and generalizable. It also makes the BST property more explicit.

## Approach 2: Range Validation (Optimal)
### Intuition
Each node must fall within a valid range `(lower, upper)`. The root has range `(-inf, +inf)`. When going left, update the upper bound to the current node's value. When going right, update the lower bound to the current node's value.

### Algorithm
1. Start with `validate(root, -inf, +inf)`.
2. For the current node, check if its value is within `(lower, upper)` (exclusive).
3. Recursively validate left child with updated upper bound: `validate(left, lower, node.val)`.
4. Recursively validate right child with updated lower bound: `validate(right, node.val, upper)`.
5. Return True only if all checks pass.

### Implementation
```python
from typing import Optional

class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right

class Solution:
    def isValidBST(self, root: Optional[TreeNode]) -> bool:
        def validate(node: Optional[TreeNode], lower: float, upper: float) -> bool:
            if not node:
                return True

            if node.val <= lower or node.val >= upper:
                return False

            return (validate(node.left, lower, node.val)
                    and validate(node.right, node.val, upper))

        return validate(root, float('-inf'), float('inf'))
```

### Complexity Analysis
- **Time Complexity**: O(n) where n is the number of nodes. Each node is visited exactly once.
- **Space Complexity**: O(h) where h is the height of the tree, for the recursion stack.

## Edge Cases
| Edge Case | Expected Behavior | Why It Matters |
|-----------|-------------------|----------------|
| Single node | Return True | A single node is always a valid BST |
| All same values | Return False | BST requires strictly less/greater |
| Values at INT_MIN/INT_MAX | Must handle correctly | Use float('-inf')/float('inf') for bounds |
| Left child valid locally but invalid globally | Return False | e.g., [5,1,4,null,null,3,6] - node 3 < 4 (local) but 3 < 5 (must be > 5) |
| Right-skewed BST | Return True if ascending | Valid if each child > parent |

## Related Problems
| Problem | Relationship | Difficulty |
|---------|-------------|------------|
| Inorder Successor in BST (#285) | Uses BST property and inorder traversal | Medium |
| Kth Smallest Element in a BST (#230) | Uses inorder traversal of BST | Medium |
| Recover Binary Search Tree (#99) | Fix a BST with two swapped nodes | Medium |
| Convert Sorted Array to BST (#108) | Construct valid BST from sorted data | Easy |

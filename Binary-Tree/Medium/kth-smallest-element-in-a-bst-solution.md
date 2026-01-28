# Kth Smallest Element in a BST

## Problem Information
- **Difficulty**: Medium
- **Category**: Binary-Tree
- **Tags**: Tree, DFS, Binary Search Tree, Binary Tree, Inorder Traversal
- **LeetCode Link**: https://leetcode.com/problems/kth-smallest-element-in-a-bst/

## Problem Statement
Given the `root` of a binary search tree, and an integer `k`, return the `k`th smallest value (1-indexed) of all the values of the nodes in the tree.

### Examples
**Example 1:**
```
Input: root = [3,1,4,null,2], k = 1
Output: 1

        3
       / \
      1   4
       \
        2
```

**Example 2:**
```
Input: root = [5,3,6,2,4,null,null,1], k = 3
Output: 3

          5
         / \
        3   6
       / \
      2   4
     /
    1
```

### Constraints
- The number of nodes in the tree is `n`.
- `1 <= k <= n <= 10^4`
- `0 <= Node.val <= 10^4`

**Follow up**: If the BST is modified often (i.e., we can do insert and delete operations) and you need to find the kth smallest frequently, how would you optimize?

## Core Concept / Pattern
An inorder traversal of a BST visits nodes in ascending order. Therefore, the kth smallest element is simply the kth node visited during an inorder traversal. We can optimize by stopping early once we have found the kth element rather than traversing the entire tree.

## Approach 1: Brute Force (Full Inorder Traversal)
### Intuition
Perform a complete inorder traversal to collect all node values in sorted order, then return the element at index `k - 1`.

### Algorithm
1. Perform inorder traversal (left -> root -> right) to collect all values.
2. Return `sorted_values[k - 1]`.

### Implementation
```python
from typing import Optional, List

class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right

class Solution:
    def kthSmallest(self, root: Optional[TreeNode], k: int) -> int:
        def inorder(node: Optional[TreeNode]) -> List[int]:
            if not node:
                return []
            return inorder(node.left) + [node.val] + inorder(node.right)

        return inorder(root)[k - 1]
```

### Complexity Analysis
- **Time Complexity**: O(n) where n is the number of nodes. We visit every node.
- **Space Complexity**: O(n) to store all values in the list, plus O(h) for the recursion stack.

### Why We Can Do Better
We do not need to traverse the entire tree. We can stop as soon as we have visited k nodes in inorder, which could be much fewer than n for small k.

## Approach 2: Optimal Solution (Iterative Inorder with Early Termination)
### Intuition
Use an iterative inorder traversal with an explicit stack. Count nodes as they are popped (visited). Stop as soon as we have visited k nodes. This avoids traversing the entire tree and uses O(h) space.

### Algorithm
1. Initialize an empty stack and set the current node to root.
2. While the stack is not empty or current is not None:
   - Push all left children onto the stack (go as far left as possible).
   - Pop a node from the stack. Decrement k.
   - If k == 0, return that node's value.
   - Move to the right child.

### Implementation
```python
from typing import Optional

class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right

class Solution:
    def kthSmallest(self, root: Optional[TreeNode], k: int) -> int:
        stack = []
        current = root

        while stack or current:
            # Go as far left as possible
            while current:
                stack.append(current)
                current = current.left

            # Process the node
            current = stack.pop()
            k -= 1
            if k == 0:
                return current.val

            # Move to right subtree
            current = current.right
```

### Complexity Analysis
- **Time Complexity**: O(H + k) where H is the height of the tree. We go down the leftmost path (O(H)) and then visit k nodes.
- **Space Complexity**: O(H) for the stack, where H is the height of the tree.

## Edge Cases
| Edge Case | Expected Behavior | Why It Matters |
|-----------|-------------------|----------------|
| k = 1 | Return the smallest (leftmost) element | Boundary case |
| k = n | Return the largest (rightmost) element | Must traverse entire tree |
| Single node, k = 1 | Return root value | Minimal tree |
| Left-skewed tree | Stack holds all nodes initially | Tests stack behavior |
| Right-skewed tree | Each pop immediately moves right | Minimal stack depth |

## Related Problems
| Problem | Relationship | Difficulty |
|---------|-------------|------------|
| Binary Tree Inorder Traversal (#94) | Core technique used here | Easy |
| Validate Binary Search Tree (#98) | Also uses BST's inorder property | Medium |
| Inorder Successor in BST (#285) | Finding the next element in BST order | Medium |
| Second Minimum Node in a Binary Tree (#671) | Finding kth element variant | Easy |

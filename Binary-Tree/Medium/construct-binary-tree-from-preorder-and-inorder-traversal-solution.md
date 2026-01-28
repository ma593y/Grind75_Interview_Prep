# Construct Binary Tree from Preorder and Inorder Traversal

## Problem Information
- **Difficulty**: Medium
- **Category**: Binary-Tree
- **Tags**: Tree, Array, Hash Table, Divide and Conquer, Binary Tree, Recursion
- **LeetCode Link**: https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/

## Problem Statement
Given two integer arrays `preorder` and `inorder` where `preorder` is the preorder traversal of a binary tree and `inorder` is the inorder traversal of the same tree, construct and return the binary tree.

### Examples
**Example 1:**
```
Input: preorder = [3,9,20,15,7], inorder = [9,3,15,20,7]
Output: [3,9,20,null,null,15,7]

        3
       / \
      9   20
         /  \
        15   7
```

**Example 2:**
```
Input: preorder = [-1], inorder = [-1]
Output: [-1]
```

### Constraints
- `1 <= preorder.length <= 3000`
- `inorder.length == preorder.length`
- `-3000 <= preorder[i], inorder[i] <= 3000`
- `preorder` and `inorder` consist of **unique** values.
- Each value of `inorder` also appears in `preorder`.
- `preorder` is **guaranteed** to be the preorder traversal of the tree.
- `inorder` is **guaranteed** to be the inorder traversal of the tree.

## Core Concept / Pattern
The first element of `preorder` is always the root. Finding that root's position in `inorder` splits the inorder array into left and right subtrees. The number of elements in the left subtree tells us how to split the preorder array as well. This divide-and-conquer approach recursively builds the entire tree. Using a hash map for O(1) root lookups in the inorder array is the key optimization.

## Approach 1: Brute Force (Recursive with Linear Search)
### Intuition
At each recursive step, take the first element of the current preorder slice as the root. Search for it in the inorder slice to determine the left and right subtree boundaries. Recurse on both subtrees.

### Algorithm
1. If preorder or inorder is empty, return `None`.
2. The root is `preorder[0]`.
3. Find the index `mid` of the root in inorder.
4. Left subtree: `preorder[1:mid+1]` and `inorder[:mid]`.
5. Right subtree: `preorder[mid+1:]` and `inorder[mid+1:]`.
6. Recursively build left and right children.

### Implementation
```python
from typing import Optional, List

class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right

class Solution:
    def buildTree(self, preorder: List[int], inorder: List[int]) -> Optional[TreeNode]:
        if not preorder or not inorder:
            return None

        root_val = preorder[0]
        root = TreeNode(root_val)
        mid = inorder.index(root_val)

        root.left = self.buildTree(preorder[1:mid + 1], inorder[:mid])
        root.right = self.buildTree(preorder[mid + 1:], inorder[mid + 1:])

        return root
```

### Complexity Analysis
- **Time Complexity**: O(n^2) in the worst case. Each recursive call does an O(n) search with `inorder.index()`, and array slicing is O(n).
- **Space Complexity**: O(n^2) due to array slicing creating new lists at each level.

### Why We Can Do Better
The linear search in `inorder.index()` can be replaced with a hash map for O(1) lookups. Array slicing can be replaced with index pointers to avoid creating new lists.

## Approach 2: Optimal Solution (Hash Map + Index Pointers)
### Intuition
Pre-build a hash map from value to index for the inorder array. Use index boundaries instead of slicing. Use a preorder index pointer that increments as we consume root values.

### Algorithm
1. Build a hash map: `{value: index}` for every element in inorder.
2. Maintain a `pre_idx` pointer starting at 0.
3. Define a recursive helper `build(in_left, in_right)`:
   - If `in_left > in_right`, return `None`.
   - The root value is `preorder[pre_idx]`. Increment `pre_idx`.
   - Look up the root's inorder index from the hash map.
   - Recursively build left subtree: `build(in_left, root_index - 1)`.
   - Recursively build right subtree: `build(root_index + 1, in_right)`.
   - Return the root node.
4. Call `build(0, len(inorder) - 1)`.

### Implementation
```python
from typing import Optional, List

class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right

class Solution:
    def buildTree(self, preorder: List[int], inorder: List[int]) -> Optional[TreeNode]:
        inorder_map = {val: idx for idx, val in enumerate(inorder)}
        self.pre_idx = 0

        def build(in_left: int, in_right: int) -> Optional[TreeNode]:
            if in_left > in_right:
                return None

            root_val = preorder[self.pre_idx]
            self.pre_idx += 1
            root = TreeNode(root_val)

            root_index = inorder_map[root_val]
            root.left = build(in_left, root_index - 1)
            root.right = build(root_index + 1, in_right)

            return root

        return build(0, len(inorder) - 1)
```

### Complexity Analysis
- **Time Complexity**: O(n) where n is the number of nodes. Each node is processed exactly once, and hash map lookups are O(1).
- **Space Complexity**: O(n) for the hash map and the recursion stack (O(h) where h is the tree height, worst case O(n) for a skewed tree).

## Edge Cases
| Edge Case | Expected Behavior | Why It Matters |
|-----------|-------------------|----------------|
| Single node | Return that node | Base case |
| Left-skewed tree | All nodes in left subtree | preorder = [1,2,3], inorder = [3,2,1] |
| Right-skewed tree | All nodes in right subtree | preorder = [1,2,3], inorder = [1,2,3] |
| Balanced tree | Proper split at each level | Standard case |
| Two nodes | One child correctly placed | Smallest non-trivial case |

## Related Problems
| Problem | Relationship | Difficulty |
|---------|-------------|------------|
| Construct Binary Tree from Inorder and Postorder (#106) | Same idea, postorder root is last element | Medium |
| Construct Binary Tree from Preorder and Postorder (#889) | Harder variant without inorder | Medium |
| Serialize and Deserialize Binary Tree (#297) | Tree reconstruction from string | Hard |
| Maximum Binary Tree (#654) | Recursive tree construction | Medium |

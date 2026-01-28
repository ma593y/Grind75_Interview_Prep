# Construct Binary Tree from Preorder and Inorder Traversal - Study Guide

## Problem Summary
- **Difficulty**: Medium
- **Category**: Binary-Tree
- **Tags**: Tree, Array, Hash Table, Divide and Conquer, Binary Tree
- **LeetCode Link**: https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/

> **One-liner**: Reconstruct a binary tree given its preorder and inorder traversal arrays, using the fact that preorder's first element is the root and its position in inorder splits left/right subtrees.

## Key Intuition
Preorder traversal visits root first, then left, then right. Inorder traversal visits left, then root, then right. The first element of preorder is always the root. Finding that root in inorder tells us the size of the left subtree (everything to its left) and right subtree (everything to its right). We can then recursively apply the same logic to build the entire tree.

## Approaches Overview
| Approach | Time | Space | Trade-off |
|----------|------|-------|-----------|
| Recursive with Array Slicing | O(n^2) | O(n^2) | Simple but slow due to slicing and linear search |
| Hash Map + Index Pointers | O(n) | O(n) | Optimal, avoids slicing and uses O(1) lookups |

## Approach 1: Recursive with Array Slicing
### Core Idea
Take `preorder[0]` as root. Find it in `inorder` to determine the split. Slice both arrays and recurse.

### Code
```python
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

### Walkthrough
Given preorder = [3, 9, 20, 15, 7], inorder = [9, 3, 15, 20, 7]:
1. Root = 3, mid = 1 in inorder
2. Left: preorder=[9], inorder=[9] -> leaf node 9
3. Right: preorder=[20,15,7], inorder=[15,20,7]
   - Root = 20, mid = 1 in [15,20,7]
   - Left: preorder=[15], inorder=[15] -> leaf 15
   - Right: preorder=[7], inorder=[7] -> leaf 7
4. Result: tree rooted at 3 with left child 9, right child 20 (children 15, 7)

## Approach 2: Hash Map + Index Pointers (Optimal)
### Core Idea
Build a hash map from inorder values to indices for O(1) lookup. Use a single preorder index pointer that increments as we consume root values. Use boundary indices instead of slicing arrays.

### Code
```python
class Solution:
    def buildTree(self, preorder: List[int], inorder: List[int]) -> Optional[TreeNode]:
        inorder_map = {val: idx for idx, val in enumerate(inorder)}
        self.pre_idx = 0

        def build(in_left, in_right):
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

### Walkthrough
Given preorder = [3, 9, 20, 15, 7], inorder = [9, 3, 15, 20, 7]:
- inorder_map = {9:0, 3:1, 15:2, 20:3, 7:4}
1. build(0, 4): root=3 (pre_idx=0->1), root_index=1
   - Left: build(0, 0): root=9 (pre_idx=1->2), root_index=0
     - build(0, -1) -> None, build(1, 0) -> None -> leaf 9
   - Right: build(2, 4): root=20 (pre_idx=2->3), root_index=3
     - Left: build(2, 2): root=15 (pre_idx=3->4), root_index=2 -> leaf 15
     - Right: build(4, 4): root=7 (pre_idx=4->5), root_index=4 -> leaf 7

**Critical detail**: Left subtree MUST be built before right subtree because `pre_idx` advances in preorder (root, left, right).

## Complexity Analysis
- Brute Force: O(n^2) time (linear search + slicing), O(n^2) space (array copies).
- Optimal: O(n) time (each node processed once with O(1) lookup), O(n) space (hash map + recursion stack).

## Common Mistakes
1. **Building right subtree before left**: The preorder index must advance through the left subtree first. Building right before left produces an incorrect tree.
2. **Off-by-one errors in boundary indices**: `in_left > in_right` is the base case, not `>=`. A single element (in_left == in_right) is valid.
3. **Forgetting that values are unique**: The problem guarantees unique values, which is essential for the hash map approach.
4. **Confusing preorder vs postorder**: For postorder, the root is the LAST element, and you build right subtree before left.

## Interview Tips
- **How to communicate**: "Preorder gives us the root first. Inorder tells us which elements belong to the left vs right subtree. I use a hash map to avoid linear search."
- **Clarifying questions**: "Are all values unique?" (Yes, per constraints.) "Can either array be empty?" (No, length >= 1.)
- **Key insight to mention**: The number of elements to the left of the root in inorder determines how many elements from preorder belong to the left subtree.
- **Follow-up**: "What if values are not unique?" (This approach would not work -- we would need additional information.)

## Mnemonics / Memory Aids
- **"Pre gives root, In gives split"**: Preorder's first element is root; finding it in inorder splits left/right.
- **"Left before right, always"**: Build left subtree first because preorder processes left before right.
- **"Hash the In, Walk the Pre"**: Hash map for inorder, walk through preorder sequentially.

## Self-Assessment Checklist
- [ ] Can I explain why preorder[0] is always the root?
- [ ] Can I draw out how inorder splits into left/right subtrees?
- [ ] Can I code the optimal solution with hash map and index pointers?
- [ ] Can I explain why left must be built before right?
- [ ] Can I adapt this approach for inorder + postorder input?

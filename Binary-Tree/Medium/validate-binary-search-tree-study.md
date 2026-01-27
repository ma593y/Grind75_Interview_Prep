# Validate Binary Search Tree - Study Guide

## Problem Summary
- **Difficulty**: Medium
- **Category**: Binary-Tree
- **Tags**: Tree, DFS, BST, Binary Search Tree, Recursion
- **LeetCode Link**: https://leetcode.com/problems/validate-binary-search-tree/

> **One-liner**: Determine if a binary tree satisfies the BST property where every node's left subtree contains only smaller values and right subtree contains only larger values.

## Key Intuition
Checking only immediate parent-child relationships is not enough. Node 3 in `[5,1,4,null,null,3,6]` is less than its parent 4, but it is in the right subtree of 5 and should be greater than 5. We need to propagate valid ranges (lower, upper bounds) downward, or verify that an inorder traversal yields a strictly increasing sequence.

## Approaches Overview
| Approach | Time | Space | Trade-off |
|----------|------|-------|-----------|
| Inorder Traversal | O(n) | O(h) | Leverages BST's sorted-order property |
| Range Validation | O(n) | O(h) | Explicitly enforces bounds, more generalizable |

## Approach 1: Inorder Traversal
### Core Idea
An inorder traversal of a valid BST produces strictly increasing values. Track the previous value and ensure each new value is strictly greater.

### Code
```python
class Solution:
    def isValidBST(self, root: Optional[TreeNode]) -> bool:
        self.prev = float('-inf')

        def inorder(node):
            if not node:
                return True
            if not inorder(node.left):
                return False
            if node.val <= self.prev:
                return False
            self.prev = node.val
            return inorder(node.right)

        return inorder(root)
```

### Walkthrough
Given tree `[5, 1, 4, null, null, 3, 6]`:
1. Inorder visits: 1, then 5, then 3, then 4, then 6
2. 1 > -inf -> OK, prev = 1
3. 5 > 1 -> OK, prev = 5
4. 3 > 5 -> FAIL! 3 <= 5 -> return False

## Approach 2: Range Validation (Optimal)
### Core Idea
Pass `(lower, upper)` bounds to each node. The root starts with `(-inf, +inf)`. Going left tightens the upper bound. Going right tightens the lower bound.

### Code
```python
class Solution:
    def isValidBST(self, root: Optional[TreeNode]) -> bool:
        def validate(node, lower, upper):
            if not node:
                return True
            if node.val <= lower or node.val >= upper:
                return False
            return (validate(node.left, lower, node.val)
                    and validate(node.right, node.val, upper))

        return validate(root, float('-inf'), float('inf'))
```

### Walkthrough
Given tree `[2, 1, 3]`:
1. validate(2, -inf, inf): 2 is in range -> check children
2. validate(1, -inf, 2): 1 is in (-inf, 2) -> OK, leaf node
3. validate(3, 2, inf): 3 is in (2, inf) -> OK, leaf node
4. All pass -> return True

Given tree `[5, 1, 4, null, null, 3, 6]`:
1. validate(5, -inf, inf): OK
2. validate(1, -inf, 5): OK
3. validate(4, 5, inf): 4 <= 5 -> FAIL! Return False

## Complexity Analysis
- Both approaches: **O(n) time**, visiting each node once.
- Both: **O(h) space** for recursion stack, where h = tree height.

## Common Mistakes
1. **Only checking parent-child**: `if node.left.val < node.val` is insufficient. You must check against the entire ancestor chain via bounds.
2. **Using `<=` and `>=` incorrectly**: BST property is strictly less/greater (no duplicates allowed in standard definition). Use `<` and `>` for comparison, or equivalently reject when `<=` lower or `>=` upper.
3. **Using int min/max as bounds**: Node values can be INT_MIN or INT_MAX. Use `float('-inf')` and `float('inf')` to avoid boundary issues.
4. **Forgetting that equal values are invalid**: `[2, 2]` is not a valid BST.

## Interview Tips
- **How to communicate**: Explain why local checking fails with a concrete example like `[5,4,6,null,null,3,7]`. Then present the range-based solution.
- **Clarifying questions to ask**: "Can there be duplicate values?" "What is the range of node values?"
- **Optimization path**: Both approaches are O(n). Range validation is more commonly expected. Mention inorder as an alternative.
- **Follow-up variations**: "What if duplicates are allowed on the left?" (Adjust the bound comparison.) "Can you do it iteratively?" (Yes, with a stack.)

## Mnemonics / Memory Aids
- **"Narrow the range"**: Going left narrows the upper bound, going right narrows the lower bound.
- **"Inorder = sorted"**: If you know inorder of BST is sorted, just verify the sorted property.

## Self-Assessment Checklist
- [ ] Can I identify this problem type within 2 minutes?
- [ ] Can I code the optimal solution without looking at notes?
- [ ] Can I explain the time/space complexity clearly?
- [ ] Can I handle all edge cases?
- [ ] Can I discuss trade-offs between approaches?

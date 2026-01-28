# Kth Smallest Element in a BST - Study Guide

## Problem Summary
- **Difficulty**: Medium
- **Category**: Binary-Tree
- **Tags**: Tree, DFS, Binary Search Tree, Inorder Traversal
- **LeetCode Link**: https://leetcode.com/problems/kth-smallest-element-in-a-bst/

> **One-liner**: Find the kth smallest value in a BST by leveraging the fact that inorder traversal of a BST produces sorted order.

## Key Intuition
A BST's inorder traversal (left, root, right) visits nodes in ascending order. So the kth smallest element is the kth node visited during inorder traversal. We do not need to sort anything -- the BST structure gives us sorted order for free.

## Approaches Overview
| Approach | Time | Space | Trade-off |
|----------|------|-------|-----------|
| Full Inorder to Array | O(n) | O(n) | Simple but traverses entire tree |
| Iterative Inorder (Early Stop) | O(H + k) | O(H) | Optimal, stops as soon as kth element found |

## Approach 1: Full Inorder to Array
### Core Idea
Traverse the entire BST inorder, collect values into a list, and return the (k-1)th element.

### Code
```python
class Solution:
    def kthSmallest(self, root: Optional[TreeNode], k: int) -> int:
        def inorder(node):
            if not node:
                return []
            return inorder(node.left) + [node.val] + inorder(node.right)

        return inorder(root)[k - 1]
```

### Walkthrough
Given BST [5, 3, 6, 2, 4, null, null, 1], k = 3:
- Inorder: [1, 2, 3, 4, 5, 6]
- k = 3 -> return element at index 2 -> **3**

## Approach 2: Iterative Inorder with Early Termination (Optimal)
### Core Idea
Use a stack for iterative inorder traversal. Count each visited node. Return immediately when the count reaches k, avoiding unnecessary traversal.

### Code
```python
class Solution:
    def kthSmallest(self, root: Optional[TreeNode], k: int) -> int:
        stack = []
        current = root

        while stack or current:
            while current:
                stack.append(current)
                current = current.left

            current = stack.pop()
            k -= 1
            if k == 0:
                return current.val

            current = current.right
```

### Walkthrough
Given BST [5, 3, 6, 2, 4, null, null, 1], k = 3:
```
          5
         / \
        3   6
       / \
      2   4
     /
    1
```
1. Push 5, 3, 2, 1 onto stack (go left). Stack: [5, 3, 2, 1]
2. Pop 1. k = 2. Move right (None).
3. Pop 2. k = 1. Move right (None).
4. Pop 3. k = 0. Return **3**.

Only visited 3 nodes instead of all 6.

## Complexity Analysis
- Full Inorder: O(n) time, O(n) space -- visits every node and stores all values.
- Iterative (Optimal): O(H + k) time, O(H) space -- goes down the leftmost path (H steps) then visits k nodes. For a balanced BST with H = log n, this is O(log n + k).

## Common Mistakes
1. **Using 0-indexed k**: The problem states k is 1-indexed. Return the element when count reaches k, not k-1.
2. **Forgetting the iterative inorder pattern**: The two-loop structure (outer while + inner while for left descent) is the standard pattern. Practice it until it is automatic.
3. **Not considering the follow-up**: If frequent kth-smallest queries with modifications, consider augmenting the BST with subtree sizes.

## Interview Tips
- **How to communicate**: "Inorder traversal of a BST gives sorted order. I will iterate inorder and stop at the kth element."
- **Clarifying questions**: "Is k always valid (1 <= k <= n)?" "Can the tree be modified between queries?"
- **Follow-up answer**: For frequent queries with tree modifications, augment each node with a count of nodes in its left subtree. This allows O(H) lookup by comparing k with the left subtree count at each node.
- **Pattern recognition**: Whenever a problem asks about ordering or ranking in a BST, think inorder traversal first.

## Mnemonics / Memory Aids
- **"BST + Inorder = Sorted"**: This is the fundamental BST property to remember.
- **"Stack-left-pop-right"**: The iterative inorder pattern: push lefts, pop and process, go right.
- **"Stop at k, not at n"**: Early termination is the key optimization.

## Self-Assessment Checklist
- [ ] Can I write the iterative inorder traversal from memory?
- [ ] Can I explain why inorder on a BST is sorted?
- [ ] Can I analyze the O(H + k) complexity?
- [ ] Can I handle the follow-up about frequent queries with tree modifications?
- [ ] Can I identify this pattern when k-th element is mentioned with BST?

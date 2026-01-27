# Maximum Depth of Binary Tree - Study Guide

## Problem Summary
- **Difficulty**: Easy
- **Category**: Binary-Tree
- **Tags**: Tree, DFS, BFS, Recursion, Binary Tree
- **LeetCode Link**: https://leetcode.com/problems/maximum-depth-of-binary-tree/

> **One-liner**: Return the number of nodes along the longest root-to-leaf path in a binary tree.

## Key Intuition
The maximum depth is defined recursively: it is 1 (for the current node) plus the maximum of the depths of the left and right subtrees. A null node has depth 0. This three-line recursive solution is one of the most elegant in all of tree problems.

## Approaches Overview
| Approach | Time | Space | Trade-off |
|----------|------|-------|-----------|
| Recursive DFS | O(n) | O(h) | Elegant and concise, uses call stack |
| Iterative BFS | O(n) | O(w) | Counts levels directly, avoids deep recursion |

## Approach 1: Recursive DFS
### Core Idea
Base case: null node returns 0. Recursive case: return `1 + max(left_depth, right_depth)`.

### Code
```python
class Solution:
    def maxDepth(self, root: Optional[TreeNode]) -> int:
        if not root:
            return 0
        return 1 + max(self.maxDepth(root.left), self.maxDepth(root.right))
```

### Walkthrough
Given tree `[3, 9, 20, null, null, 15, 7]`:
1. At node 3: maxDepth(9) and maxDepth(20)
2. Node 9: no children -> returns 1
3. Node 20: maxDepth(15) = 1, maxDepth(7) = 1 -> returns 2
4. Node 3: 1 + max(1, 2) = 3

## Approach 2: Iterative BFS (Optimal)
### Core Idea
Use level-order traversal with a queue. Each complete level processed increments a depth counter. The final value is the answer.

### Code
```python
from collections import deque

class Solution:
    def maxDepth(self, root: Optional[TreeNode]) -> int:
        if not root:
            return 0
        queue = deque([root])
        depth = 0
        while queue:
            for _ in range(len(queue)):
                node = queue.popleft()
                if node.left:
                    queue.append(node.left)
                if node.right:
                    queue.append(node.right)
            depth += 1
        return depth
```

### Walkthrough
Given tree `[3, 9, 20, null, null, 15, 7]`:
1. Level 1: queue = [3], process node 3, add 9 and 20 -> depth = 1
2. Level 2: queue = [9, 20], process both, add 15 and 7 -> depth = 2
3. Level 3: queue = [15, 7], process both, no children -> depth = 3
4. Queue empty -> return 3

## Complexity Analysis
- Both approaches: **O(n) time**, visiting every node exactly once.
- Recursive DFS: O(h) space (recursion stack), where h is tree height.
- Iterative BFS: O(w) space (queue), where w is max tree width.

## Common Mistakes
1. **Off-by-one error**: Returning 0 for a single node instead of 1. A single node has depth 1.
2. **Confusing depth and height**: In this problem, depth = height = number of nodes on the longest root-to-leaf path.
3. **Forgetting the null check**: Not handling the empty tree case leads to attribute errors.

## Interview Tips
- **How to communicate**: This is often the first tree problem asked. State the recursive definition clearly.
- **Clarifying questions to ask**: "Is depth measured in nodes or edges?" (LeetCode measures in nodes.) "Can the tree be empty?"
- **Optimization path**: The recursive DFS is already optimal. Mention BFS as an alternative. Both are O(n).
- **Follow-up variations**: "What about minimum depth?" "What about N-ary tree?" "Can you do it iteratively?"

## Mnemonics / Memory Aids
- **"1 + max of children"**: The entire algorithm in four words.
- **"Null is zero, leaf is one"**: Base case produces 0, a leaf node returns 1 (0 + 0 + 1).

## Self-Assessment Checklist
- [ ] Can I identify this problem type within 2 minutes?
- [ ] Can I code the optimal solution without looking at notes?
- [ ] Can I explain the time/space complexity clearly?
- [ ] Can I handle all edge cases?
- [ ] Can I discuss trade-offs between approaches?

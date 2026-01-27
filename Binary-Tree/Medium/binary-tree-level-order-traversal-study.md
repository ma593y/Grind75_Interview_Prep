# Binary Tree Level Order Traversal - Study Guide

## Problem Summary
- **Difficulty**: Medium
- **Category**: Binary-Tree
- **Tags**: Tree, BFS, Binary Tree, Queue
- **LeetCode Link**: https://leetcode.com/problems/binary-tree-level-order-traversal/

> **One-liner**: Return the values of a binary tree grouped by level, from left to right, top to bottom.

## Key Intuition
BFS with a queue naturally processes nodes level by level. The critical technique is to snapshot the queue size at the start of each level, then process exactly that many nodes. This cleanly separates nodes into their respective levels without any extra bookkeeping.

## Approaches Overview
| Approach | Time | Space | Trade-off |
|----------|------|-------|-----------|
| DFS with Depth Tracking | O(n) | O(n) | Works but less intuitive for level-order |
| BFS with Queue | O(n) | O(n) | Natural fit, clear level boundaries |

## Approach 1: DFS with Depth Tracking
### Core Idea
Pass the depth as a parameter during DFS. Use the depth as an index into the result list. Create a new sub-list whenever we reach a new depth for the first time.

### Code
```python
class Solution:
    def levelOrder(self, root: Optional[TreeNode]) -> List[List[int]]:
        result = []

        def dfs(node, depth):
            if not node:
                return
            if depth == len(result):
                result.append([])
            result[depth].append(node.val)
            dfs(node.left, depth + 1)
            dfs(node.right, depth + 1)

        dfs(root, 0)
        return result
```

### Walkthrough
Given tree `[3, 9, 20, null, null, 15, 7]`:
1. dfs(3, 0): result = [[3]]
2. dfs(9, 1): result = [[3], [9]]
3. dfs(20, 1): result = [[3], [9, 20]]
4. dfs(15, 2): result = [[3], [9, 20], [15]]
5. dfs(7, 2): result = [[3], [9, 20], [15, 7]]

## Approach 2: BFS with Queue (Optimal)
### Core Idea
Use a queue. For each level, record the queue size, dequeue that many nodes (adding their values to the current level), and enqueue their children.

### Code
```python
from collections import deque

class Solution:
    def levelOrder(self, root: Optional[TreeNode]) -> List[List[int]]:
        if not root:
            return []
        result = []
        queue = deque([root])
        while queue:
            level = []
            for _ in range(len(queue)):
                node = queue.popleft()
                level.append(node.val)
                if node.left:
                    queue.append(node.left)
                if node.right:
                    queue.append(node.right)
            result.append(level)
        return result
```

### Walkthrough
Given tree `[3, 9, 20, null, null, 15, 7]`:
1. Queue: [3], level_size = 1 -> process 3, enqueue 9, 20 -> level = [3]
2. Queue: [9, 20], level_size = 2 -> process 9 (no children), process 20 (enqueue 15, 7) -> level = [9, 20]
3. Queue: [15, 7], level_size = 2 -> process 15 and 7 (leaves) -> level = [15, 7]
4. Result: [[3], [9, 20], [15, 7]]

## Complexity Analysis
- Both approaches: **O(n) time**, visiting each node once.
- DFS: O(h) recursion stack + O(n) result.
- BFS: O(w) queue (max width) + O(n) result. Overall O(n) for both.

## Common Mistakes
1. **Not snapshotting the queue size**: If you check `len(queue)` inside the loop, the size changes as you add children. Capture it before the inner loop.
2. **Forgetting the empty tree check**: Return `[]` for a null root.
3. **Adding None nodes to the queue**: Always check if children exist before enqueuing.

## Interview Tips
- **How to communicate**: Start with "BFS is the natural fit for level-order." Explain the queue-size snapshot technique.
- **Clarifying questions to ask**: "Should I return values grouped by level?" "Can the tree be empty?"
- **Optimization path**: Both DFS and BFS are O(n). BFS is preferred because the traversal order matches the output structure.
- **Follow-up variations**: "Zigzag level order?" (Alternate append direction.) "Bottom-up level order?" (Reverse the result.) "Right side view?" (Last element per level.)

## Mnemonics / Memory Aids
- **"Snapshot, process, collect"**: Snapshot the queue size, process that many nodes, collect their values.
- **"BFS = Breadth = By-level"**: BFS inherently groups nodes by level.

## Self-Assessment Checklist
- [ ] Can I identify this problem type within 2 minutes?
- [ ] Can I code the optimal solution without looking at notes?
- [ ] Can I explain the time/space complexity clearly?
- [ ] Can I handle all edge cases?
- [ ] Can I discuss trade-offs between approaches?

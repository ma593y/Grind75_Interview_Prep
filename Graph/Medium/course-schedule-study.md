# Course Schedule - Study Guide

## Problem Summary
- **Difficulty**: Medium
- **Category**: Graph
- **Tags**: Topological Sort, BFS, DFS, Directed Graph, Cycle Detection
- **LeetCode Link**: https://leetcode.com/problems/course-schedule/

> **One-liner**: Determine if all courses can be completed given prerequisite pairs, which reduces to detecting whether a directed graph has a cycle.

## Key Intuition
Each course is a node and each prerequisite is a directed edge. If the graph has a cycle, some courses form a circular dependency and cannot all be completed. Two classic cycle detection methods work: DFS with 3-state coloring (unvisited/in-progress/completed), or Kahn's algorithm (BFS topological sort using in-degrees). If every node can be processed, there is no cycle.

## Approaches Overview
| Approach | Time | Space | Trade-off |
|----------|------|-------|-----------|
| DFS with 3-Color Marking | O(V + E) | O(V + E) | Recursive, elegant cycle detection |
| Kahn's Algorithm (BFS Topological Sort) | O(V + E) | O(V + E) | Iterative, no stack overflow risk, produces ordering |

## Approach 1: DFS with 3-Color Marking
### Core Idea
Mark nodes as unvisited (0), in-progress (1), or completed (2). If DFS encounters an in-progress node, a back edge (cycle) exists. If it encounters a completed node, that subtree is already verified.

### Code
```python
class Solution:
    def canFinish(self, numCourses: int, prerequisites: list[list[int]]) -> bool:
        graph = [[] for _ in range(numCourses)]
        for course, prereq in prerequisites:
            graph[prereq].append(course)

        state = [0] * numCourses  # 0=unvisited, 1=in-progress, 2=completed

        def dfs(node):
            if state[node] == 1:
                return False  # cycle
            if state[node] == 2:
                return True   # already safe
            state[node] = 1
            for neighbor in graph[node]:
                if not dfs(neighbor):
                    return False
            state[node] = 2
            return True

        for course in range(numCourses):
            if not dfs(course):
                return False
        return True
```

### Walkthrough
Given numCourses=4, prerequisites=[[1,0],[2,0],[3,1],[3,2]]:
- Graph: 0->[1,2], 1->[3], 2->[3]
- DFS(0): mark 0 in-progress -> DFS(1): mark 1 in-progress -> DFS(3): mark 3 in-progress -> no neighbors -> mark 3 completed. Back to DFS(1): mark 1 completed. DFS(2): mark 2 in-progress -> DFS(3): already completed -> mark 2 completed. Mark 0 completed.
- All courses reachable, no cycles -> return True.

## Approach 2: Kahn's Algorithm (BFS Topological Sort) (Optimal)
### Core Idea
Compute in-degrees. Nodes with in-degree 0 have no prerequisites. Process them, reduce neighbors' in-degrees, and repeat. If all nodes are processed, no cycle exists.

### Code
```python
from collections import deque

class Solution:
    def canFinish(self, numCourses: int, prerequisites: list[list[int]]) -> bool:
        graph = [[] for _ in range(numCourses)]
        in_degree = [0] * numCourses

        for course, prereq in prerequisites:
            graph[prereq].append(course)
            in_degree[course] += 1

        queue = deque(i for i in range(numCourses) if in_degree[i] == 0)
        processed = 0

        while queue:
            node = queue.popleft()
            processed += 1
            for neighbor in graph[node]:
                in_degree[neighbor] -= 1
                if in_degree[neighbor] == 0:
                    queue.append(neighbor)

        return processed == numCourses
```

### Walkthrough
Given numCourses=4, prerequisites=[[1,0],[2,0],[3,1],[3,2]]:
- in_degree: [0, 1, 1, 2]. Queue starts with [0].
- Process 0: processed=1. Reduce in-degree of 1->0 and 2->0. Queue=[1,2].
- Process 1: processed=2. Reduce in-degree of 3->1. Queue=[2].
- Process 2: processed=3. Reduce in-degree of 3->0. Queue=[3].
- Process 3: processed=4. Queue empty.
- 4 == 4 -> True.

## Complexity Analysis
- Both approaches: **O(V + E)** time, where V = numCourses and E = number of prerequisites.
- **O(V + E)** space for the adjacency list. DFS adds O(V) for the state array and recursion stack. Kahn's adds O(V) for the in-degree array and queue.

## Common Mistakes
1. **Using only 2 states in DFS (visited/unvisited)**: Two states cannot distinguish between a back edge (cycle) and a cross edge (already-completed node). You need 3 states.
2. **Building the graph in the wrong direction**: `[a, b]` means b must come before a, so the edge is b -> a, not a -> b.
3. **Forgetting disconnected components**: Both DFS and Kahn's must handle nodes that are not connected. DFS: loop through all nodes. Kahn's: count processed nodes.
4. **Not starting Kahn's with ALL zero in-degree nodes**: Missing some starting nodes leads to incomplete processing.

## Interview Tips
- **How to communicate**: "This is cycle detection in a directed graph. I will use Kahn's algorithm -- compute in-degrees, process zero in-degree nodes first, and check if all nodes are processed."
- **Clarifying questions**: "Can there be duplicate prerequisites?" (All pairs are unique.) "Can a course be its own prerequisite?"
- **Why Kahn's is often preferred**: It is iterative (no recursion limit concerns), and it naturally produces a valid course ordering (useful for Course Schedule II).
- **Pattern recognition**: Any problem about ordering with dependencies is topological sort.

## Mnemonics / Memory Aids
- **"Cycle = Stuck"**: If there is a cycle, you can never take those courses because each depends on another.
- **"Zero in-degree = Ready to take"**: Courses with no remaining prerequisites can be taken immediately.
- **"3 colors: White, Gray, Black"**: Unvisited, in-progress (current DFS path), completed.

## Self-Assessment Checklist
- [ ] Can I model this problem as a graph (nodes, edges, direction)?
- [ ] Can I implement both DFS 3-color and Kahn's algorithm from memory?
- [ ] Can I explain why 2-state DFS is insufficient for directed cycle detection?
- [ ] Can I trace through an example with Kahn's algorithm step by step?
- [ ] Can I extend this to Course Schedule II (return the ordering)?

# Course Schedule

## Problem Information
- **Difficulty**: Medium
- **Category**: Graph
- **Tags**: Topological Sort, BFS, DFS, Directed Graph, Cycle Detection
- **LeetCode Link**: https://leetcode.com/problems/course-schedule/

## Problem Statement
There are a total of `numCourses` courses you have to take, labeled from `0` to `numCourses - 1`. You are given an array `prerequisites` where `prerequisites[i] = [ai, bi]` indicates that you **must** take course `bi` first if you want to take course `ai`.

For example, the pair `[0, 1]` indicates that to take course `0` you have to first take course `1`.

Return `true` if you can finish all courses. Otherwise, return `false`.

### Examples
**Example 1:**
```
Input: numCourses = 2, prerequisites = [[1,0]]
Output: true
Explanation: There are 2 courses. To take course 1 you should have finished course 0. So it is possible.
```

**Example 2:**
```
Input: numCourses = 2, prerequisites = [[1,0],[0,1]]
Output: false
Explanation: There are 2 courses. To take course 1 you should have finished course 0, and to take
course 0 you should have finished course 1. So it is impossible.
```

### Constraints
- `1 <= numCourses <= 2000`
- `0 <= prerequisites.length <= 5000`
- `prerequisites[i].length == 2`
- `0 <= ai, bi < numCourses`
- All the pairs `prerequisites[i]` are **unique**.

## Core Concept / Pattern
This problem is equivalent to detecting whether a **directed graph has a cycle**. Each course is a node, and each prerequisite `[a, b]` is a directed edge from `b` to `a` (must take `b` before `a`). If the graph has a cycle, it is impossible to complete all courses. Two classic approaches exist: **DFS with 3-color marking** (white/gray/black) and **Kahn's algorithm** (BFS topological sort using in-degrees).

## Approach 1: DFS with Cycle Detection (3-Color)
### Intuition
Use DFS and mark each node with one of three states: unvisited (white), in the current DFS path (gray), or fully processed (black). If we encounter a gray node during DFS, we have found a back edge, which means a cycle exists.

### Algorithm
1. Build an adjacency list from prerequisites.
2. Maintain a `state` array: 0 = unvisited, 1 = in-progress, 2 = completed.
3. For each unvisited node, run DFS.
4. In DFS: mark node as in-progress (1). For each neighbor, if in-progress return `False` (cycle). If unvisited, recurse. Mark node as completed (2). Return `True`.
5. If all DFS calls return `True`, return `True`.

### Implementation
```python
def canFinish(numCourses: int, prerequisites: list[list[int]]) -> bool:
    graph = [[] for _ in range(numCourses)]
    for course, prereq in prerequisites:
        graph[prereq].append(course)

    # 0 = unvisited, 1 = in-progress, 2 = completed
    state = [0] * numCourses

    def dfs(node):
        if state[node] == 1:
            return False  # cycle detected
        if state[node] == 2:
            return True   # already fully processed

        state[node] = 1  # mark in-progress

        for neighbor in graph[node]:
            if not dfs(neighbor):
                return False

        state[node] = 2  # mark completed
        return True

    for course in range(numCourses):
        if not dfs(course):
            return False

    return True
```

### Complexity Analysis
- **Time Complexity**: O(V + E) -- each node and edge visited once.
- **Space Complexity**: O(V + E) -- adjacency list + recursion stack.

### Why We Can Do Better
This DFS solution is already optimal in terms of time complexity. However, Kahn's algorithm (BFS) is often preferred in interviews because it is iterative (no stack overflow risk) and naturally produces a topological ordering.

## Approach 2: Kahn's Algorithm - BFS Topological Sort (Optimal)
### Intuition
Compute the in-degree of every node. Nodes with in-degree 0 have no prerequisites and can be taken immediately. Process them, reduce the in-degrees of their neighbors, and repeat. If all nodes are processed, no cycle exists. If some nodes remain, there is a cycle.

### Algorithm
1. Build an adjacency list and compute in-degrees for all nodes.
2. Initialize a queue with all nodes having in-degree 0.
3. While the queue is not empty:
   - Dequeue a node, increment a counter.
   - For each neighbor, decrement its in-degree. If it becomes 0, enqueue it.
4. Return `True` if the counter equals `numCourses`.

### Implementation
```python
from collections import deque

def canFinish(numCourses: int, prerequisites: list[list[int]]) -> bool:
    graph = [[] for _ in range(numCourses)]
    in_degree = [0] * numCourses

    for course, prereq in prerequisites:
        graph[prereq].append(course)
        in_degree[course] += 1

    queue = deque()
    for i in range(numCourses):
        if in_degree[i] == 0:
            queue.append(i)

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

### Complexity Analysis
- **Time Complexity**: O(V + E) -- each node and edge processed once.
- **Space Complexity**: O(V + E) -- adjacency list, in-degree array, and queue.

## Edge Cases
| Edge Case | Expected Behavior | Why It Matters |
|-----------|-------------------|----------------|
| No prerequisites | Return True | All courses are independent |
| Single course | Return True | Trivial case |
| Self-loop `[0, 0]` | Return False | Course requires itself |
| Two courses with mutual dependency | Return False | Simple cycle |
| Disconnected graph components | Must check all components | DFS/BFS from all nodes |
| Linear chain of prerequisites | Return True | No cycle, just a long chain |

## Related Problems
| Problem | Relationship | Difficulty |
|---------|-------------|------------|
| Course Schedule II (LC #210) | Same problem but return the ordering | Medium |
| Alien Dictionary (LC #269) | Topological sort on character ordering | Hard |
| Minimum Height Trees (LC #310) | Degree-based BFS peeling, similar to Kahn's | Medium |
| Parallel Courses (LC #1136) | Topological sort with level tracking | Medium |

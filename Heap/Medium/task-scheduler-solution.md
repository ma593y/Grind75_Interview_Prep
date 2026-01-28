# Task Scheduler

## Problem Information
- **Difficulty**: Medium
- **Category**: Heap / Priority Queue
- **Tags**: Array, Hash Table, Greedy, Sorting, Heap, Counting
- **LeetCode Link**: https://leetcode.com/problems/task-scheduler/

## Problem Statement

You are given an array of CPU tasks, each represented by letters A to Z, and a cooling interval `n`. Each cycle or interval allows the completion of one task. Tasks can be completed in any order, but there's a constraint: **identical tasks must be separated by at least `n` intervals** due to cooling time.

Return the **minimum number of intervals** the CPU will take to finish all the given tasks.

### Examples

**Example 1:**
```
Input: tasks = ["A","A","A","B","B","B"], n = 2
Output: 8
Explanation: A possible sequence is: A -> B -> idle -> A -> B -> idle -> A -> B.
After completing task A, you must wait two intervals before doing A again.
```

**Example 2:**
```
Input: tasks = ["A","C","A","B","D","B"], n = 1
Output: 6
Explanation: A possible sequence is: A -> B -> A -> C -> B -> D.
With a cooling interval of 1, there is no need for idle time.
```

**Example 3:**
```
Input: tasks = ["A","A","A","B","B","B"], n = 0
Output: 6
Explanation: No cooling needed, all tasks can run consecutively.
```

### Constraints
- `1 <= tasks.length <= 10^4`
- `tasks[i]` is an uppercase English letter.
- `0 <= n <= 100`

## Core Concept / Pattern

The key insight is that the most frequent task determines the minimum time. The most frequent task creates a "frame" of `(max_freq - 1)` groups, each of size `(n + 1)`. The remaining slots are filled with other tasks or idle time. The answer is the maximum of this frame-based calculation and the total number of tasks (when there are enough diverse tasks to fill all slots).

## Approach 1: Brute Force (Simulation with Heap)

### Intuition
Simulate the scheduling process. At each time step, pick the task with the highest remaining count that is not in cooldown. Use a max-heap for efficient selection and a cooldown queue.

### Algorithm
1. Count task frequencies.
2. Push all frequencies into a max-heap (negated for Python).
3. Use a queue to track tasks in cooldown with their available time.
4. At each time step:
   - If a cooldown task becomes available, push it back to the heap.
   - Pop the most frequent available task, decrement its count.
   - If count > 0, add to cooldown queue with available time = current_time + n + 1.
5. Continue until heap and queue are both empty.

### Implementation

```python
import heapq
from collections import Counter, deque

class Solution:
    def leastInterval(self, tasks: list[str], n: int) -> int:
        counts = Counter(tasks)
        max_heap = [-cnt for cnt in counts.values()]
        heapq.heapify(max_heap)

        time = 0
        cooldown = deque()  # (available_time, neg_count)

        while max_heap or cooldown:
            time += 1

            if cooldown and cooldown[0][0] == time:
                heapq.heappush(max_heap, cooldown.popleft()[1])

            if max_heap:
                cnt = heapq.heappop(max_heap) + 1  # +1 because negated
                if cnt != 0:
                    cooldown.append((time + n + 1, cnt))
            # else: idle cycle

        return time
```

### Complexity Analysis
- **Time Complexity**: O(T * 26) where T is the total intervals. Each step involves heap operations on at most 26 elements.
- **Space Complexity**: O(26) = O(1) for the heap and cooldown queue.

### Why We Can Do Better
The simulation runs step-by-step. We can compute the answer mathematically in O(N) using the frequency formula.

## Approach 2: Optimal Solution (Math / Greedy Formula)

### Intuition
The most frequent task determines the structure. If the most frequent task appears `max_freq` times, it creates `(max_freq - 1)` gaps of size `(n + 1)` (one slot for the task itself plus n cooldown slots). The last group only has tasks with frequency equal to `max_freq`.

The formula is:
```
intervals = (max_freq - 1) * (n + 1) + count_of_max_freq_tasks
```

But if there are enough diverse tasks to fill all idle slots, the answer is simply the total number of tasks.

### Algorithm
1. Count task frequencies.
2. Find `max_freq` (the highest frequency).
3. Count how many tasks have this maximum frequency (`max_count`).
4. Calculate: `result = (max_freq - 1) * (n + 1) + max_count`.
5. Return `max(result, len(tasks))`.

### Implementation

```python
from collections import Counter

class Solution:
    def leastInterval(self, tasks: list[str], n: int) -> int:
        counts = Counter(tasks)
        max_freq = max(counts.values())
        max_count = sum(1 for freq in counts.values() if freq == max_freq)

        # Frame: (max_freq - 1) groups of (n + 1), plus final group of max_count
        result = (max_freq - 1) * (n + 1) + max_count

        # If enough tasks to fill all gaps, no idle time needed
        return max(result, len(tasks))
```

### Complexity Analysis
- **Time Complexity**: O(N) where N is the number of tasks (one pass to count, one to find max).
- **Space Complexity**: O(1) since there are at most 26 distinct tasks.

## Edge Cases

| Edge Case | Expected Behavior | Why It Matters |
|-----------|-------------------|----------------|
| n = 0 | Return len(tasks) | No cooldown, all tasks run back-to-back |
| All same task `["A","A","A"]`, n=2 | `(3-1)*(2+1)+1 = 7` | Maximum idle time |
| All distinct tasks | Return len(tasks) | No idle time needed |
| Single task | Return 1 | Simplest case |
| Multiple tasks with same max frequency | max_count > 1 affects last group | Critical for correct formula |

## Related Problems

| Problem | Relationship | Difficulty |
|---------|-------------|------------|
| Rearrange String K Distance Apart (LC #358) | Similar cooldown constraint with ordering | Hard |
| Reorganize String (LC #767) | Place characters with gap 1 | Medium |
| Maximum Frequency Stack (LC #895) | Frequency-based priority | Hard |
| CPU Task Scheduling | Real-world scheduling variant | -- |

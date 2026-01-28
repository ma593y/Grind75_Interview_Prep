# Task Scheduler - Study Guide

## Problem Summary
> **One-liner**: Find the minimum number of intervals (including idle) to execute all tasks when identical tasks must be separated by at least n intervals.

## Key Intuition

The most frequent task dictates the schedule. Imagine laying out the most frequent task with n gaps between each occurrence. Those gaps form "slots" that other tasks can fill. If there are not enough other tasks to fill the slots, the CPU idles. If there are more than enough, no idle time is needed and the answer is simply the total number of tasks.

## Approaches Overview

| Approach | Time | Space | Trade-off |
|----------|------|-------|-----------|
| Simulation (Heap + Queue) | O(T * 26) | O(26) | Intuitive but slower |
| Math/Greedy Formula | O(N) | O(1) | Elegant, instant computation |

## Approach 1: Simulation with Max-Heap

### Core Idea
Simulate time step by step. Always pick the most frequent available task (greedy). Use a max-heap for selection and a cooldown queue for tasks waiting to become available.

### Code

```python
import heapq
from collections import Counter, deque

class Solution:
    def leastInterval(self, tasks: list[str], n: int) -> int:
        counts = Counter(tasks)
        max_heap = [-cnt for cnt in counts.values()]
        heapq.heapify(max_heap)

        time = 0
        cooldown = deque()

        while max_heap or cooldown:
            time += 1

            if cooldown and cooldown[0][0] == time:
                heapq.heappush(max_heap, cooldown.popleft()[1])

            if max_heap:
                cnt = heapq.heappop(max_heap) + 1
                if cnt != 0:
                    cooldown.append((time + n + 1, cnt))

        return time
```

### Walkthrough
Using `tasks = ["A","A","A","B","B","B"], n = 2`:
```
Counts: A=3, B=3
Heap: [-3, -3]

time=1: pop -3 (A), cnt=-2, cooldown=[(4,-2)], heap=[-3]
time=2: pop -3 (B), cnt=-2, cooldown=[(4,-2),(5,-2)], heap=[]
time=3: idle (heap empty, nothing available)
time=4: B available, push -2. pop -2 (A), cnt=-1, cooldown=[(5,-2),(7,-1)]
time=5: A available, push -2. pop -2 (B), cnt=-1, cooldown=[(7,-1),(8,-1)]
time=6: idle
time=7: push -1, pop -1 (A), cnt=0, cooldown=[(8,-1)]
time=8: push -1, pop -1 (B), cnt=0

Total: 8
```

## Approach 2: Math/Greedy Formula (Optimal)

### Core Idea
The most frequent task creates a frame:
```
[A _ _ ] [A _ _ ] [A]
```
Frame has `(max_freq - 1)` full groups of size `(n + 1)`, plus a final partial group containing all tasks with max frequency.

Formula: `max((max_freq - 1) * (n + 1) + max_count, len(tasks))`

### Code

```python
from collections import Counter

class Solution:
    def leastInterval(self, tasks: list[str], n: int) -> int:
        counts = Counter(tasks)
        max_freq = max(counts.values())
        max_count = sum(1 for freq in counts.values() if freq == max_freq)

        result = (max_freq - 1) * (n + 1) + max_count
        return max(result, len(tasks))
```

### Walkthrough
Using `tasks = ["A","A","A","B","B","B"], n = 2`:
```
Counts: A=3, B=3
max_freq = 3
max_count = 2 (both A and B have frequency 3)

result = (3 - 1) * (2 + 1) + 2 = 2 * 3 + 2 = 8

Visual:
[A B _] [A B _] [A B]
 ^---^   ^---^   ^-^
 group1  group2  final (only max_count tasks)

max(8, 6) = 8
```

Another example: `tasks = ["A","B","C","D","E","F"], n = 2`:
```
max_freq = 1, max_count = 6
result = (1 - 1) * 3 + 6 = 6
max(6, 6) = 6  -- no idle time, enough diverse tasks
```

## Complexity Analysis

| Aspect | Simulation | Formula |
|--------|-----------|---------|
| Time | O(total_intervals * 26) | O(N) |
| Space | O(26) | O(26) = O(1) |
| When to use | When you need the actual schedule | When you only need the count |

## Common Mistakes

1. **Forgetting max(result, len(tasks))**: When there are many diverse tasks, the formula gives a number smaller than the total tasks. The answer is always at least len(tasks).
2. **Off-by-one in frame calculation**: The frame has `max_freq - 1` full groups (not max_freq), because the last occurrence doesn't need a gap after it.
3. **Confusing max_count**: This is the number of tasks that have the maximum frequency, not the maximum frequency itself.
4. **In simulation, not handling idle correctly**: When the heap is empty but cooldown has items, it is an idle cycle.

## Interview Tips

- **Present the math approach**: It is O(N) and shows mathematical thinking.
- **Draw the frame**: Visualize `[A _ _] [A _ _] [A]` to explain the formula.
- **Explain the max()**: "Either we have enough tasks to fill all slots (answer = len(tasks)) or we don't (answer = formula)."
- **Know the simulation as backup**: If the interviewer wants to see the actual schedule, the simulation approach builds it.
- **Greedy principle**: "Always schedule the most frequent available task to minimize idle time."

## Mnemonics / Memory Aids

- **"Frame and Fill"**: The most frequent task creates the frame. Other tasks fill the gaps. Remaining gaps become idle.
- **"(f-1)(n+1)+c"**: f = max frequency, n = cooldown, c = count of tasks with max frequency.
- **"Max of formula and total"**: The answer is never less than the total number of tasks.

## Self-Assessment Checklist

- [ ] Can I derive the math formula from scratch?
- [ ] Can I implement the heap simulation approach?
- [ ] Do I understand why the answer is `max(formula, len(tasks))`?
- [ ] Can I draw the "frame" visualization for a given example?
- [ ] Can I explain what `max_count` represents and why it matters?
- [ ] Can I handle the case where n = 0?
- [ ] Can I trace through the simulation for a small example?

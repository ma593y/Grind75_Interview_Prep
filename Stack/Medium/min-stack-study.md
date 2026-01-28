# Min Stack - Study Guide

## Problem Summary
- **Difficulty**: Medium / **Category**: Stack / **Tags**: Stack, Design / **LeetCode Link**: https://leetcode.com/problems/min-stack/

> **One-liner**: Design a stack that supports push, pop, top, and retrieving the minimum element, all in O(1) time.

## Key Intuition

The minimum can only change when elements are pushed or popped. By storing the current minimum alongside each element (or in a parallel stack), we create a "snapshot" of the minimum at every stack depth. Popping an element automatically restores the minimum from the previous state. This is a classic example of trading space for time.

## Approaches Overview

| Approach | Time (all ops) | Space | Trade-off |
|----------|---------------|-------|-----------|
| Scan for min on getMin | O(n) for getMin, O(1) others | O(n) | Simple but getMin is slow |
| Two stacks (parallel min tracking) | O(1) for all | O(n) | Optimal -- extra stack for min snapshots |
| Single stack with tuples | O(1) for all | O(n) | Same idea, slightly different implementation |

## Approach 1: Scan on getMin

### Core Idea
Use a plain stack. For `getMin`, scan the entire stack with `min()`. Simple but O(n) per query.

### Code

```python
class MinStack:
    def __init__(self):
        self.stack = []

    def push(self, val: int) -> None:
        self.stack.append(val)

    def pop(self) -> None:
        self.stack.pop()

    def top(self) -> int:
        return self.stack[-1]

    def getMin(self) -> int:
        return min(self.stack)
```

### Walkthrough
Push -2, 0, -3. getMin scans [-2, 0, -3] and returns -3. Pop. getMin scans [-2, 0] and returns -2. Each getMin is O(n).

## Approach 2: Two Stacks (Optimal)

### Core Idea
Maintain a `min_stack` in parallel with the main `stack`. On each push, `min_stack` stores the running minimum. On pop, both stacks pop together. `getMin` simply returns the top of `min_stack`.

### Code

```python
class MinStack:
    def __init__(self):
        self.stack = []
        self.min_stack = []

    def push(self, val: int) -> None:
        self.stack.append(val)
        if self.min_stack:
            self.min_stack.append(min(val, self.min_stack[-1]))
        else:
            self.min_stack.append(val)

    def pop(self) -> None:
        self.stack.pop()
        self.min_stack.pop()

    def top(self) -> int:
        return self.stack[-1]

    def getMin(self) -> int:
        return self.min_stack[-1]
```

### Walkthrough
Operations: push(-2), push(0), push(-3), getMin, pop, top, getMin

- push(-2): stack=[-2], min_stack=[-2]. Min is -2.
- push(0): stack=[-2,0], min_stack=[-2,-2]. min(0, -2) = -2.
- push(-3): stack=[-2,0,-3], min_stack=[-2,-2,-3]. min(-3, -2) = -3.
- getMin(): return min_stack[-1] = -3.
- pop(): stack=[-2,0], min_stack=[-2,-2]. Minimum restored to -2.
- top(): return stack[-1] = 0.
- getMin(): return min_stack[-1] = -2.

**Alternative: Single stack with tuples** -- store `(val, current_min)` pairs:

```python
class MinStack:
    def __init__(self):
        self.stack = []  # Each entry: (value, min_at_this_level)

    def push(self, val: int) -> None:
        current_min = min(val, self.stack[-1][1]) if self.stack else val
        self.stack.append((val, current_min))

    def pop(self) -> None:
        self.stack.pop()

    def top(self) -> int:
        return self.stack[-1][0]

    def getMin(self) -> int:
        return self.stack[-1][1]
```

## Complexity Analysis

- **Time**: O(1) for all operations -- push, pop, top, and getMin each perform a constant number of operations.
- **Space**: O(n) -- The min_stack (or tuple storage) adds O(n) extra space. In the best case (ascending values), min_stack entries are all the same first value, but space is still O(n).

## Common Mistakes

1. **Not handling the first push correctly**: The min_stack is empty before the first push. Must check `if self.min_stack` before comparing with `min_stack[-1]`.
2. **Forgetting to pop from min_stack**: Both stacks must always be the same size. Forgetting to pop the min_stack desynchronizes them.
3. **Using a single min variable instead of a stack**: A single variable loses history. After popping the minimum element, you would not know the previous minimum without rescanning.
4. **Trying to optimize space by only pushing to min_stack when a new min appears**: This is possible but requires careful handling of duplicate minimums. The simpler approach of always pushing to min_stack avoids subtle bugs.

## Interview Tips

- **How to communicate**: Start by observing that `getMin` in O(1) requires storing min history, not just a single variable. Explain the "snapshot" concept: each stack level remembers its minimum.
- **Clarifying questions to ask**: "Will pop/top/getMin always be called on a non-empty stack?" (Yes). "What is the range of values?" (Full 32-bit integer range, including negatives).
- **Optimization path**: Brute force O(n) getMin -> O(1) getMin with auxiliary min stack. For a follow-up, discuss space optimization: only push to min_stack when a new minimum is encountered (requires handling duplicate mins).
- **Follow-up variations**: "Design a Max Stack with popMax" (LeetCode #716 -- requires a different approach using doubly linked list + tree map). "Design a Min Queue" (use two min-stacks as in the queue-from-stacks problem).

## Mnemonics / Memory Aids

- **"Mirror stack for min"**: The min_stack mirrors the main stack, recording the running minimum at each depth.
- **"Snapshot at every push"**: Think of each min_stack entry as a "photograph" of the minimum at that moment.
- **"Both push, both pop"**: The two stacks are always in sync -- same number of elements, pushed and popped together.

## Self-Assessment Checklist
- [ ] Can I identify this problem type within 2 minutes?
- [ ] Can I code the optimal solution without looking at notes?
- [ ] Can I explain the time/space complexity clearly?
- [ ] Can I handle all edge cases?
- [ ] Can I discuss trade-offs between approaches?

# Implement Queue using Stacks - Study Guide

## Problem Summary
- **Difficulty**: Easy / **Category**: Stack / **Tags**: Stack, Design, Queue / **LeetCode Link**: https://leetcode.com/problems/implement-queue-using-stacks/

> **One-liner**: Implement a FIFO queue using only two LIFO stacks with amortized O(1) operations.

## Key Intuition

Pouring elements from one stack into another reverses their order, converting LIFO into FIFO. By using an "input" stack for pushes and an "output" stack for pops, and only transferring when the output stack is empty, each element is moved between stacks at most once. This gives amortized O(1) time for all queue operations.

## Approaches Overview

| Approach | Time (push) | Time (pop) | Space | Trade-off |
|----------|-------------|------------|-------|-----------|
| Transfer on every pop | O(1) | O(n) | O(n) | Simple but wasteful transfers |
| Lazy transfer (two-stack) | O(1) | O(1) amortized | O(n) | Optimal amortized performance |

## Approach 1: Transfer on Every Pop

### Core Idea
Push to `stack1`. For pop/peek, move everything to `stack2`, get the answer, move everything back. This ensures `stack1` always has elements in push order.

### Code

```python
class MyQueue:
    def __init__(self):
        self.stack1 = []
        self.stack2 = []

    def push(self, x: int) -> None:
        self.stack1.append(x)

    def pop(self) -> int:
        while self.stack1:
            self.stack2.append(self.stack1.pop())
        result = self.stack2.pop()
        while self.stack2:
            self.stack1.append(self.stack2.pop())
        return result

    def peek(self) -> int:
        while self.stack1:
            self.stack2.append(self.stack1.pop())
        result = self.stack2[-1]
        while self.stack2:
            self.stack1.append(self.stack2.pop())
        return result

    def empty(self) -> bool:
        return len(self.stack1) == 0
```

### Walkthrough
Push 1, 2, 3. Pop:
- stack1 = [1, 2, 3]. Transfer to stack2 = [3, 2, 1]. Pop 1. Transfer back: stack1 = [2, 3].

## Approach 2: Lazy Transfer (Optimal)

### Core Idea
Use `input_stack` for pushes and `output_stack` for pops. Only transfer from input to output when output is empty. Elements in `output_stack` are already in FIFO order (reversed), so they can be popped directly.

### Code

```python
class MyQueue:
    def __init__(self):
        self.input_stack = []
        self.output_stack = []

    def push(self, x: int) -> None:
        self.input_stack.append(x)

    def pop(self) -> int:
        self._transfer_if_needed()
        return self.output_stack.pop()

    def peek(self) -> int:
        self._transfer_if_needed()
        return self.output_stack[-1]

    def empty(self) -> bool:
        return not self.input_stack and not self.output_stack

    def _transfer_if_needed(self) -> None:
        if not self.output_stack:
            while self.input_stack:
                self.output_stack.append(self.input_stack.pop())
```

### Walkthrough
Operations: push(1), push(2), pop(), push(3), pop(), pop()
- push(1): input=[1], output=[]
- push(2): input=[1,2], output=[]
- pop(): output is empty -> transfer. input=[], output=[2,1]. Pop -> returns 1. output=[2].
- push(3): input=[3], output=[2]
- pop(): output is not empty -> pop directly. Returns 2. output=[].
- pop(): output is empty -> transfer. input=[], output=[3]. Pop -> returns 3.

All returns in FIFO order: 1, 2, 3.

## Complexity Analysis

- **Time**: O(1) amortized for all operations -- Each element is pushed to input_stack once (O(1)), transferred to output_stack once (O(1) amortized), and popped from output_stack once (O(1)). Total cost per element: O(1) amortized.
- **Space**: O(n) -- All n elements are distributed across the two stacks.

## Common Mistakes

1. **Transferring when output_stack is NOT empty**: This would scramble the FIFO order. Only transfer when `output_stack` is empty.
2. **Forgetting to check both stacks for `empty()`**: The queue is empty only when *both* stacks are empty, not just one.
3. **Not understanding amortized analysis**: A single pop can be O(n), but averaged over n operations, each pop is O(1). Interviewers expect you to explain this.
4. **Using non-stack operations**: The problem restricts you to push-to-top, pop-from-top, peek-top, size, and is-empty. Do not index into the middle of a stack.

## Interview Tips

- **How to communicate**: Draw two stacks and show how elements flow: push goes to the input stack, and elements are poured into the output stack (reversing order) only when needed.
- **Clarifying questions to ask**: "Are pop and peek always called on a non-empty queue?" (Yes per constraints). "What data types are stored?" (Integers).
- **Optimization path**: Explain the naive approach (transfer on every pop, O(n) per pop) then the lazy transfer (amortized O(1)). Clearly explain the amortized analysis.
- **Follow-up variations**: "Implement a stack using queues" (LeetCode #225). "Can you achieve worst-case O(1) for all operations?" (Not with only two stacks -- amortized O(1) is the best possible).

## Mnemonics / Memory Aids

- **"Input stack, Output stack"**: Push always goes to input. Pop/peek always come from output.
- **"Pour only when dry"**: Only transfer (pour) from input to output when output is empty (dry).
- **"Double reversal = original order"**: Push to input (order: 1,2,3 bottom-to-top). Pour to output (order: 3,2,1 bottom-to-top). Pop from output top gives 1,2,3 = FIFO.

## Self-Assessment Checklist
- [ ] Can I identify this problem type within 2 minutes?
- [ ] Can I code the optimal solution without looking at notes?
- [ ] Can I explain the time/space complexity clearly?
- [ ] Can I handle all edge cases?
- [ ] Can I discuss trade-offs between approaches?

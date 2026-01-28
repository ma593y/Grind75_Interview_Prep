# Implement Queue using Stacks

## Problem Information
- **Difficulty**: Easy
- **Category**: Stack
- **Tags**: Stack, Design, Queue
- **LeetCode Link**: https://leetcode.com/problems/implement-queue-using-stacks/

## Problem Statement

Implement a first in first out (FIFO) queue using only two stacks. The implemented queue should support all the functions of a normal queue (`push`, `peek`, `pop`, and `empty`).

Implement the `MyQueue` class:
- `void push(int x)` -- Pushes element `x` to the back of the queue.
- `int pop()` -- Removes the element from the front of the queue and returns it.
- `int peek()` -- Returns the element at the front of the queue.
- `boolean empty()` -- Returns `true` if the queue is empty, `false` otherwise.

**Notes:**
- You must use **only** standard operations of a stack, which means only `push to top`, `peek/pop from top`, `size`, and `is empty` operations are valid.

### Examples

**Example 1:**
```
Input:
["MyQueue", "push", "push", "peek", "pop", "empty"]
[[], [1], [2], [], [], []]
Output: [null, null, null, 1, 1, false]

Explanation:
MyQueue myQueue = new MyQueue();
myQueue.push(1); // queue is: [1]
myQueue.push(2); // queue is: [1, 2]
myQueue.peek();  // return 1
myQueue.pop();   // return 1, queue is: [2]
myQueue.empty(); // return false
```

### Constraints
- `1 <= x <= 9`
- At most `100` calls will be made to `push`, `pop`, `peek`, and `empty`.
- All the calls to `pop` and `peek` are valid (i.e., the queue is not empty when called).

## Core Concept / Pattern

A stack is LIFO (Last-In-First-Out) and a queue is FIFO (First-In-First-Out). To reverse the order, we can pour elements from one stack into another -- this reversal converts LIFO order into FIFO order. The key design decision is *when* to transfer elements between the two stacks, which leads to two different approaches with different amortized costs.

Using two stacks -- an "input" stack for pushes and an "output" stack for pops -- we only transfer elements from input to output when the output stack is empty. This lazy transfer gives us amortized O(1) for all operations.

## Approach 1: Brute Force (Transfer on Every Pop)

### Intuition

Always push to `stack1`. For pop/peek, transfer all elements from `stack1` to `stack2` (reversing the order), get the front element, then transfer everything back to `stack1`.

### Algorithm

1. `push(x)`: Push `x` onto `stack1`.
2. `pop()`: Move all elements from `stack1` to `stack2`. Pop from `stack2` (this is the front). Move everything back to `stack1`. Return the popped value.
3. `peek()`: Same as pop but without removing the element.
4. `empty()`: Return whether `stack1` is empty.

### Implementation

```python
class MyQueue:
    def __init__(self):
        self.stack1 = []
        self.stack2 = []

    def push(self, x: int) -> None:
        self.stack1.append(x)

    def pop(self) -> int:
        # Transfer all to stack2
        while self.stack1:
            self.stack2.append(self.stack1.pop())
        # Pop the front element
        result = self.stack2.pop()
        # Transfer back
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

### Complexity Analysis
- **Time Complexity**: O(1) for `push` and `empty`. O(n) for `pop` and `peek` where n is the number of elements.
- **Space Complexity**: O(n) -- Both stacks together hold all n elements.

### Why We Can Do Better

Transferring elements back and forth on every pop/peek is wasteful. If we leave the elements in the output stack, subsequent pops can be served directly from there without any transfers.

## Approach 2: Optimal Solution (Amortized O(1) with Lazy Transfer)

### Intuition

Maintain two stacks: `input_stack` for pushes and `output_stack` for pops/peeks. Only transfer elements from `input_stack` to `output_stack` when `output_stack` is empty. Since each element is transferred at most once, all operations are amortized O(1).

### Algorithm

1. `push(x)`: Push `x` onto `input_stack`.
2. `pop()`: If `output_stack` is empty, transfer all elements from `input_stack`. Pop from `output_stack`.
3. `peek()`: If `output_stack` is empty, transfer all elements from `input_stack`. Return the top of `output_stack`.
4. `empty()`: Return whether both stacks are empty.

### Implementation

```python
class MyQueue:
    def __init__(self):
        self.input_stack = []   # For push operations
        self.output_stack = []  # For pop/peek operations

    def push(self, x: int) -> None:
        self.input_stack.append(x)

    def pop(self) -> int:
        self._transfer_if_needed()
        return self.output_stack.pop()

    def peek(self) -> int:
        self._transfer_if_needed()
        return self.output_stack[-1]

    def empty(self) -> bool:
        return len(self.input_stack) == 0 and len(self.output_stack) == 0

    def _transfer_if_needed(self) -> None:
        if not self.output_stack:
            while self.input_stack:
                self.output_stack.append(self.input_stack.pop())
```

### Complexity Analysis
- **Time Complexity**: O(1) amortized for all operations. Each element is pushed and popped from each stack at most once, so over n operations, the total work is O(n), giving O(1) amortized per operation. Worst case for a single `pop` is O(n) when a transfer is needed.
- **Space Complexity**: O(n) -- Both stacks together hold all n elements.

## Edge Cases

| Edge Case | Expected Behavior | Why It Matters |
|-----------|-------------------|----------------|
| Single push then pop | Returns the pushed element | Simplest operation sequence |
| Multiple pushes then multiple pops | Returns elements in FIFO order | Core queue behavior |
| Interleaved push and pop | Maintains FIFO order | Tests lazy transfer correctness |
| Push, peek, push, pop | Peek returns first element, pop returns first element | Peek should not remove element |
| Pop until empty | Returns all elements in FIFO order | Tests complete draining |

## Related Problems

| Problem | Relationship | Difficulty |
|---------|-------------|------------|
| Implement Stack using Queues (LeetCode #225) | Reverse direction: implement stack with queues | Easy |
| Design Circular Queue (LeetCode #622) | Queue design with fixed capacity | Medium |
| Design Circular Deque (LeetCode #641) | Double-ended queue design | Medium |

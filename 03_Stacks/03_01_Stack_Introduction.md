
---
# Stack Introduction

From our study of linked lists and arrays, we have seen data structures that let you access any element — you can go to index 5 of an array or traverse to any node. But many real problems do not need arbitrary access. They need something simpler and more disciplined — a structure where you can only work with the _most recently added_ element.

Think of a stack of plates. You add plates on top, and you take plates from the top. You never pull a plate from the middle — that would topple everything. The last plate you put on is the first one you take off. This is the **stack** data structure.

> **Definition.** A _stack_ is an Abstract Data Type that follows the **Last In First Out (LIFO)** principle — the last element inserted is the first one removed.

A stack supports exactly three core operations:

|Operation|Description|Complexity|
|---|---|---|
|`push(x)`|Insert element $x$ on top|$O(1)$|
|`pop()`|Remove and return the top element|$O(1)$|
|`peek()` / `top()`|Return top element without removing|$O(1)$|
|`isEmpty()`|Check if stack is empty|$O(1)$|

Everything is $O(1)$ — this is the power of the constraint. By restricting access to only the top, every operation is instant.

---

## Where Stacks Appear Naturally

The LIFO property is not arbitrary — it mirrors how many real processes work:

**Function call stack** — when `main` calls `foo` which calls `bar`, the most recent call (`bar`) must finish before `foo` can continue. The operating system literally uses a stack to track this — it is why infinite recursion causes a _stack overflow_.

**Undo/Redo** — every text editor maintains a stack of actions. Ctrl+Z pops the most recent action and reverses it.

**Browser history** — the back button pops the most recently visited page.

**Expression evaluation** — compilers use stacks to parse and evaluate arithmetic expressions.

All of these share the same pattern — _the most recently seen thing is the next thing to be processed_. Whenever you notice this pattern in a problem, a stack is likely the right tool.

> 📖 For how the call stack works at the hardware level: [CS:APP — Bryant and O'Hallaron, Chapter 3](http://csapp.cs.cmu.edu/)


---
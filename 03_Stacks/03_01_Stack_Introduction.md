
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

---

And finally `03_00_Stacks.md`:

---

# Stacks

## Notes

- [[03_01_Stack_Introduction]]
- [[03_02_Stack_Implementation]]
- [[03_03_Monotonic_Stack]]

## Applications

- [[03_04_00_Applications]]

## Quiz

- [[03_QQ_Stacks_Quiz]]

---

## Quick Reference

### Stack ADT

> **Definition.** A stack is a LIFO (Last In First Out) data structure. The last element inserted is the first one removed.

|Operation|Complexity|
|---|---|
|push|$O(1)$|
|pop|$O(1)$|
|peek / top|$O(1)$|
|isEmpty|$O(1)$|

---

### Implementation Comparison

||Static Array|Dynamic Array|Linked List|
|---|---|---|---|
|Push|$O(1)$|$O(1)$ amortized|$O(1)$|
|Pop|$O(1)$|$O(1)$|$O(1)$|
|Size limit|Fixed|Dynamic|Dynamic|
|Cache|Good|Good|Poor|

---

### Monotonic Stack — The Four Variants

|Problem|Stack order|Pop condition|Record when|
|---|---|---|---|
|NGE|Decreasing|$arr[top] < curr$|On pop|
|NSE|Increasing|$arr[top] > curr$|On pop|
|PGE|Decreasing|$arr[top] \leq curr$|Before push|
|PSE|Increasing|$arr[top] \geq curr$|Before push|

$$\text{span}[i] = i - \text{index of PGE}[i]$$

$$\text{Rectangle area at } i = h[i] \times (\text{NSE}[i] - \text{PSE}[i] - 1)$$

$$\text{Water at } i = \min(\text{maxLeft}[i],\ \text{maxRight}[i]) - h[i]$$

---

### Operator Precedence for Infix Conversion

|Operator|Precedence|
|---|---|
|`^`|3 (highest)|
|`*`, `/`|2|
|`+`, `-`|1 (lowest)|

---
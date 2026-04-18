Starting with `04_01_Queue_Introduction.md`:

---

# Queue Introduction

From our study of stacks, we saw a structure that enforces LIFO — the last thing in is the first thing out. But many real situations work the opposite way. When you stand in a line at a ticket counter, the first person to arrive is the first person served. The person who just joined cannot cut to the front. This is the **queue** data structure.

> **Definition.** A _queue_ is an Abstract Data Type that follows the **First In First Out (FIFO)** principle — the first element inserted is the first one removed.

A queue has two ends — elements enter from the **rear** (also called back or tail) and leave from the **front** (also called head). You can only add to one end and remove from the other.

|Operation|Description|Complexity|
|---|---|---|
|`enqueue(x)`|Insert element $x$ at the rear|$O(1)$|
|`dequeue()`|Remove and return element from front|$O(1)$|
|`front()`|Return front element without removing|$O(1)$|
|`isEmpty()`|Check if queue is empty|$O(1)$|

---

## Where Queues Appear Naturally

The FIFO property mirrors any real-world waiting line:

**CPU Scheduling** — processes waiting to be executed by the CPU are managed in a queue. The first process to request CPU time is the first to get it.

**BFS (Breadth First Search)** — the most important algorithmic application of queues. When exploring a graph level by level, nodes discovered at level $k$ are processed before nodes at level $k+1$. A queue naturally maintains this order. You will see this in [[10_02_01_BFS_and_Connected_Components]].

**Printer spooler** — print jobs are queued and processed in the order they arrive.

**Keyboard buffer** — keystrokes are stored in a queue and processed in order.

**Producer-Consumer problems** — one process produces data, another consumes it. A queue sits between them as a buffer.

The key distinction from a stack — in a stack the most _recently_ seen item is processed next, in a queue the most _distantly_ seen item is processed next. This makes queues natural for level-by-level or time-ordered processing.

> 📖 For CPU scheduling with queues: [Operating Systems Three Easy Pieces — Scheduling](https://pages.cs.wisc.edu/~remzi/OSTEP/cpu-sched.pdf)

---

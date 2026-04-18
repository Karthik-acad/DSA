
And finally `04_00_Queues.md`:

---

# Queues

## Notes

- [[04_01_Queue_Introduction]]
- [[04_02_Queue_Implementation]]
- [[04_03_Circular_Queue]]
- [[04_04_Deque_Double_Ended_Queue]]

## Applications

- [[04_05_00_Applications]]

## Quiz

- [[04_QQ_Queues_Quiz]]

---

## Quick Reference

### Queue ADT

> **Definition.** A queue is a FIFO (First In First Out) data structure. The first element inserted is the first one removed.

|Operation|Complexity|
|---|---|
|enqueue|$O(1)$|
|dequeue|$O(1)$|
|front|$O(1)$|
|isEmpty|$O(1)$|

---

### Implementation Comparison

| Implementation | Naive Array | Circular Array | Linked List | std::queue |
| -------------- | ----------- | -------------- | ----------- | ---------- |
| Enqueue        | $O(1)$      | $O(1)$         | $O(1)$      | $O(1)$     |
| Dequeue        | $O(1)$      | $O(1)$         | $O(1)$      | $O(1)$     |
| Space waste    | Yes         | No             | No          | No         |
| Size limit     | Fixed       | Fixed          | Dynamic     | Dynamic    |

Circular array key formula: $$\text{rear} = (\text{rear} + 1) \mod \text{capacity}$$ $$\text{front} = (\text{front} + 1) \mod \text{capacity}$$

---

### Deque — All Four Operations $O(1)$

|Operation|Stack equivalent|Queue equivalent|
|---|---|---|
|push_back + pop_back|Stack|—|
|push_back + pop_front|—|Queue|
|push_front + pop_front|Stack (reversed)|—|

---

### Sliding Window Maximum

Monotonic decreasing deque — front holds index of current maximum:

- Pop front if outside window: $\text{front} < i - k + 1$
- Pop back if smaller than incoming: $arr[\text{back}] < arr[i]$

**Complexity:** Time $O(n)$, Space $O(k)$

---

### Queue vs Stack

| Type            | Queue                       | Stack                        |
| --------------- | --------------------------- | ---------------------------- |
| Order           | FIFO                        | LIFO                         |
| Graph traversal | BFS — level by level        | DFS — depth first            |
| Shortest path   | Yes (unweighted)            | No                           |
| Use when        | Processing in arrival order | Processing most recent first |

---

---

# Priority Queue Introduction

From our study of queues, we know that a regular queue serves elements in the order they arrived — FIFO. But what if arrival order does not matter and instead each element has a _priority_, and the element with the highest priority should always be served first regardless of when it arrived?

Think of a hospital emergency room. Patients do not get treated in the order they walked in — a patient with a heart attack gets seen before someone with a sprained ankle, even if they arrived later. The priority (severity) determines the order, not arrival time.

This is exactly what a **priority queue** does.

> **Definition.** A _priority queue_ is an Abstract Data Type where each element has an associated priority, and the element with the highest (or lowest) priority is always accessed and removed first, regardless of insertion order.

|Operation|Description|Complexity|
|---|---|---|
|`insert(x, p)`|Insert element $x$ with priority $p$|$O(\log n)$|
|`extractMax()` / `extractMin()`|Remove and return highest/lowest priority element|$O(\log n)$|
|`peek()`|Return highest/lowest priority element without removing|$O(1)$|
|`isEmpty()`|Check if empty|$O(1)$|

Notice insert and extract are $O(\log n)$ — not $O(1)$ like a regular queue. This is the cost of maintaining the priority ordering. The underlying data structure that makes this efficient is the **heap**.

---

## Naive Implementations and Why They Fail

Before looking at heaps, consider why simpler implementations are insufficient:

|Implementation|Insert|ExtractMax|Problem|
|---|---|---|---|
|Unsorted array|$O(1)$|$O(n)$ — scan all|Extract too slow|
|Sorted array|$O(n)$ — shift|$O(1)$ — last element|Insert too slow|
|Sorted linked list|$O(n)$ — find position|$O(1)$|Insert too slow|
|**Binary Heap**|$O(\log n)$|$O(\log n)$|Balanced tradeoff|

The heap gives us $O(\log n)$ for both — a balanced tradeoff that makes priority queues practical for large inputs.

---

## Where Priority Queues Appear

**Dijkstra's shortest path** — always process the unvisited node with the smallest known distance. A min priority queue maintains this efficiently. You will see this in [[10_03_01_Dijkstras_Algorithm]].

**Huffman encoding** — always merge the two lowest frequency nodes. A min priority queue selects them in $O(\log n)$. You will see this in [[14_04_Huffman_Encoding]].

**CPU Scheduling** — processes with higher priority get CPU time first.

**Heap Sort** — repeatedly extract the maximum to sort in $O(n \log n)$.

**K largest / K smallest problems** — maintain a heap of size $k$ to find the top $k$ elements efficiently.

> 📖 For priority queue applications in competitive programming: [CP Algorithms — Priority Queue](https://cp-algorithms.com/data_structures/priority_queue.html)

---

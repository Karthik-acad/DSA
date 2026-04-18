
And finally `05_00_Priority_Queues_and_Heaps.md`:

---

# Priority Queues and Heaps

## Notes

- [[05_01_Priority_Queue_Introduction]]
- [[05_02_Binary_Heaps_Min_Max]]
- [[05_03_Insertion_and_Deletion]]
- [[05_04_Heap_Sort]]
- [[05_05_K_Largest_K_Smallest]]
- [[05_06_Median_in_Data_Stream]]

## Quiz

- [[05_QQ_Priority_Queues_Quiz]]

---

## Quick Reference

### Priority Queue ADT

> **Definition.** A priority queue serves elements in order of priority, not arrival. Highest (or lowest) priority element is always at the front.

|Operation|Time|
|---|---|
|insert|$O(\log n)$|
|extractMax / extractMin|$O(\log n)$|
|peek|$O(1)$|

---

### Heap Array Index Formulas (0-indexed)

$$\text{parent}(i) = \lfloor (i-1)/2 \rfloor$$ $$\text{leftChild}(i) = 2i + 1$$ $$\text{rightChild}(i) = 2i + 2$$ $$\text{last internal node} = \lfloor n/2 \rfloor - 1$$ $$\text{height} = \lfloor \log_2 n \rfloor$$

---

### Heap Operations

|Operation|Time|Method|
|---|---|---|
|Insert|$O(\log n)$|Add at end, heapify-up|
|Extract max/min|$O(\log n)$|Swap root with last, heapify-down|
|Peek|$O(1)$|root = heap[0]|
|Build heap|$O(n)$|Heapify-down from $\lfloor n/2 \rfloor - 1$ to 0|
|Heap sort|$O(n \log n)$|Build heap + $n$ extractions|

---

### Min vs Max Heap

||Max-Heap|Min-Heap|
|---|---|---|
|Root|Maximum|Minimum|
|C++ STL|`priority_queue<int>`|`priority_queue<int, vector<int>, greater<int>>`|
|Use for|K largest, Heap Sort|K smallest, Dijkstra|

---

### K Largest / K Smallest

|Approach|Time|Space|Best when|
|---|---|---|---|
|Sort|$O(n \log n)$|$O(1)$|$k \approx n$|
|Heap of size $k$|$O(n \log k)$|$O(k)$|$k \ll n$|
|QuickSelect|$O(n)$ avg|$O(1)$|Single query|

---

### Median in Data Stream

Two heaps — lower max-heap + upper min-heap:

$$\text{median} = \begin{cases} \text{lower.top()} & \text{if } |\text{lower}| > |\text{upper}| \ \text{upper.top()} & \text{if } |\text{upper}| > |\text{lower}| \ \frac{\text{lower.top()} + \text{upper.top()}}{2} & \text{if sizes equal} \end{cases}$$

Insert invariant: lower.top() $\leq$ upper.top() and $\big||\text{lower}| - |\text{upper}|\big| \leq 1$

---
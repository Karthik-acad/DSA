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

Here is `04_QQ_Queues_Quiz.md`:

---

## Quiz — Queues

**Q1 — Identify the Complexity**

```cpp
queue<int> q;
for (int i = 0; i < n; i++) q.push(i);
while (!q.empty()) {
    cout << q.front();
    q.pop();
}
```

- [ ] $O(n)$
- [ ] $\Theta(n)$
- [ ] $O(n^2)$
- [ ] $O(1)$

> [!answer]- Answer O(n) and Theta(n) are both correct. n pushes and n pops each costing O(1) gives Theta(n) exactly. O(n^2) is a valid but loose upper bound. O(1) is false.

---

**Q2 — Trace the Output**

```cpp
queue<int> q;
q.push(1); q.push(2); q.push(3);
cout << q.front(); q.pop();
q.push(4);
cout << q.front(); q.pop();
cout << q.front();
```

> [!answer]- Answer Prints: 1 2 3. Queue after pushes: [1,2,3] front to back. Pop gives 1. Push 4: [2,3,4]. Pop gives 2. Front is now 3. Compare with the stack version in the stacks quiz — same operations but opposite order because FIFO vs LIFO.

---

**Q3 — True or False** A circular queue with capacity 5 that has had 100 enqueue and 95 dequeue operations performed on it has 5 elements and is full.

- [ ] True
- [ ] False

> [!answer]- Answer True. 100 enqueued minus 95 dequeued = 5 elements remaining. Capacity is 5 so it is full. The circular queue's whole point is that those 95 dequeued slots have been reused by subsequent enqueues — a naive linear array would have reported overflow long before 100 operations.

---

**Q4 — Sliding Window Maximum by hand** Array $[2, 7, 3, 1, 5, 8, 4]$, $k = 3$. Find the maximum of each window.

> [!answer]- Answer Windows and maximums: [2,7,3] → 7. [7,3,1] → 7. [3,1,5] → 5. [1,5,8] → 8. [5,8,4] → 8. Result: [7, 7, 5, 8, 8]. Deque trace: i=0: dq=[0]. i=1: 7>2 pop 0, dq=[1]. i=2: 3<7 push, dq=[1,2], output arr[1]=7. i=3: 1<3 push, dq=[1,2,3], output arr[1]=7. i=4: 5>1 pop 3, 5>3 pop 2, dq=[1,4], output arr[1]=7? No — front=1 is within window [2,4], arr[1]=7 but wait arr[4]=5<7. Output 7. Hmm — i=4 window is [3,1,5] max is 5. Front index 1 is outside window (i-k+1=2), so pop front. dq=[4], output arr[4]=5. Correct.

---

**Q5 — Design Question** How would you implement a queue using two stacks? What is the amortized complexity of enqueue and dequeue?

> [!answer]- Answer
> 
> ```cpp
> class QueueUsingStacks {
>     stack<int> inbox, outbox;
> public:
>     void enqueue(int x) { inbox.push(x); }
>     int dequeue() {
>         if (outbox.empty()) {
>             while (!inbox.empty()) {
>                 outbox.push(inbox.top());
>                 inbox.pop();
>             }
>         }
>         int val = outbox.top(); outbox.pop();
>         return val;
>     }
> };
> ```
> 
> Enqueue is always O(1). Dequeue is O(n) worst case (when outbox is empty and all n elements transfer) but amortized O(1) — each element is moved from inbox to outbox exactly once across its lifetime, so n dequeue operations cost at most 2n total moves.

---

**Q6 — BFS** Given this adjacency list (0-indexed, undirected): 0: [1, 2] 1: [0, 3] 2: [0, 4] 3: [1] 4: [2]

Trace BFS from node 0. What order are nodes visited?

> [!answer]- Answer Order: 0, 1, 2, 3, 4. Queue trace: start with [0] visited={0}. Pop 0, push neighbors 1,2: queue=[1,2] visited={0,1,2}. Pop 1, push unvisited neighbor 3: queue=[2,3] visited={0,1,2,3}. Pop 2, push unvisited neighbor 4: queue=[3,4] visited={0,1,2,3,4}. Pop 3, no unvisited neighbors. Pop 4, no unvisited neighbors. Done.

---

**Q7 — Spot the Bug**

```cpp
CircularQueue q(5);
for (int i = 0; i < 5; i++) q.enqueue(i);
q.dequeue();
q.dequeue();
q.enqueue(5);
q.enqueue(6);
q.enqueue(7);  // should this work?
```

> [!answer]- Answer Yes it should work — and will work correctly with a proper circular queue implementation. After 5 enqueues: full. After 2 dequeues: size=3, two slots freed at front. After 3 more enqueues: size=5, those freed slots reused via wraparound. Total capacity still 5. If someone implemented this with a naive linear array instead, the third enqueue after dequeuing would incorrectly report overflow even though only 3 of 5 logical slots are used — this is exactly the front-creep bug that circular queues fix.

---

**Q8 — Write it Yourself** Implement a deque from scratch using a doubly linked list supporting push_front, push_back, pop_front, pop_back, all in $O(1)$.

> [!answer]- Answer
> 
> ```cpp
> struct Node { int data; Node *prev, *next; Node(int v): data(v), prev(nullptr), next(nullptr){} };
> class Deque {
>     Node *head, *tail;
> public:
>     Deque(): head(nullptr), tail(nullptr){}
>     void push_front(int x) {
>         Node* n = new Node(x);
>         if (!head) { head = tail = n; return; }
>         n->next = head; head->prev = n; head = n;
>     }
>     void push_back(int x) {
>         Node* n = new Node(x);
>         if (!tail) { head = tail = n; return; }
>         tail->next = n; n->prev = tail; tail = n;
>     }
>     int pop_front() {
>         if (!head) return -1;
>         int val = head->data;
>         Node* temp = head;
>         head = head->next;
>         if (head) head->prev = nullptr; else tail = nullptr;
>         delete temp; return val;
>     }
>     int pop_back() {
>         if (!tail) return -1;
>         int val = tail->data;
>         Node* temp = tail;
>         tail = tail->prev;
>         if (tail) tail->next = nullptr; else head = nullptr;
>         delete temp; return val;
>     }
> };
> ```
> 
> All four operations are O(1) — no traversal needed since we maintain both head and tail pointers. This is exactly why a doubly linked list is the natural backing structure for a deque.

---

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

||Naive Array|Circular Array|Linked List|std::queue|
|---|---|---|---|---|
|Enqueue|$O(1)$|$O(1)$|$O(1)$|$O(1)$|
|Dequeue|$O(1)$|$O(1)$|$O(1)$|$O(1)$|
|Space waste|Yes|No|No|No|
|Size limit|Fixed|Fixed|Dynamic|Dynamic|

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

||Queue|Stack|
|---|---|---|
|Order|FIFO|LIFO|
|Graph traversal|BFS — level by level|DFS — depth first|
|Shortest path|Yes (unweighted)|No|
|Use when|Processing in arrival order|Processing most recent first|

---
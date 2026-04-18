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

Here is `04_02_Queue_Implementation.md`:

---

# Queue Implementation

Just like the stack, the Queue ADT can be implemented in multiple ways. The naive array implementation has a subtle problem worth understanding — and fixing it leads directly to the circular queue idea.

---

## Implementation 1 — Naive Array (and its problem)

```cpp
#include <iostream>
using namespace std;

class Queue {
    int arr[1000];
    int front, rear;
public:
    Queue() : front(0), rear(0) {}

    void enqueue(int x) {
        if (rear == 1000) { cout << "Queue full" << endl; return; }
        arr[rear++] = x;
    }

    int dequeue() {
        if (isEmpty()) return -1;
        return arr[front++];
    }

    int peek() { return isEmpty() ? -1 : arr[front]; }
    bool isEmpty() { return front == rear; }
    int size() { return rear - front; }
};
```

The problem — after many enqueue and dequeue operations, `front` creeps rightward and the slots before it are wasted. Even if the queue has only a few elements, `rear` might hit 1000 and report full when most of the array is actually free. The fix is the **circular queue**.

---

## Implementation 2 — Linked List

Each enqueue adds a node at the tail, each dequeue removes from the head. Maintain both `head` and `tail` pointers for $O(1)$ at both ends.

```cpp
#include <iostream>
using namespace std;

struct Node {
    int data;
    Node* next;
    Node(int val) : data(val), next(nullptr) {}
};

class Queue {
    Node* head;
    Node* tail;
    int sz;
public:
    Queue() : head(nullptr), tail(nullptr), sz(0) {}

    // O(1)
    void enqueue(int x) {
        Node* newNode = new Node(x);
        if (!tail) { head = tail = newNode; sz++; return; }
        tail->next = newNode;
        tail = newNode;
        sz++;
    }

    // O(1)
    int dequeue() {
        if (isEmpty()) return -1;
        int val = head->data;
        Node* temp = head;
        head = head->next;
        if (!head) tail = nullptr;
        delete temp;
        sz--;
        return val;
    }

    int peek() { return isEmpty() ? -1 : head->data; }
    bool isEmpty() { return head == nullptr; }
    int size() { return sz; }
};
```

---

## Implementation 3 — std::queue

In practice use C++'s built-in queue, backed by `std::deque` by default.

```cpp
#include <iostream>
#include <queue>
using namespace std;

int main() {
    queue<int> q;

    q.push(1);   // enqueue
    q.push(2);
    q.push(3);

    cout << q.front() << endl;  // 1
    q.pop();                     // dequeue
    cout << q.front() << endl;  // 2
    cout << q.size() << endl;   // 2
    cout << q.empty() << endl;  // 0 (false)

    return 0;
}
```

The **linked list** implementation is what we will primarily use going forward since it has no size limit and strict $O(1)$ for all operations with no amortized cost.

---

## Implementation Comparison

| |Naive Array|Linked List|std::queue|
|---|---|---|---|
|Enqueue|$O(1)$|$O(1)$|$O(1)$ amortized|
|Dequeue|$O(1)$|$O(1)$|$O(1)$|
|Space waste|Yes — front creep|No|No|
|Size limit|Fixed|Dynamic|Dynamic|
|Cache|Good|Poor|Good|

> 📖 For std::queue internals: [CPP Reference — std::queue](https://en.cppreference.com/w/cpp/container/queue)

---

Here is `04_03_Circular_Queue.md`:

---

# Circular Queue

The circular queue fixes the front-creep problem of the naive array implementation by treating the array as a ring — when `rear` reaches the end, it wraps around to index 0 if space is available there.

> **Definition.** A _circular queue_ is a queue implemented using a fixed-size array where the rear and front pointers wrap around using modulo arithmetic, reusing freed slots at the beginning of the array.

Think of it like a circular conveyor belt — items leave from one point and new items can immediately fill those vacated spots as the belt continues rotating.

```cpp
#include <iostream>
using namespace std;

class CircularQueue {
    int arr[1000];
    int front, rear, sz, capacity;
public:
    CircularQueue(int cap) : front(0), rear(0), sz(0), capacity(cap) {}

    // O(1)
    void enqueue(int x) {
        if (isFull()) { cout << "Queue full" << endl; return; }
        arr[rear] = x;
        rear = (rear + 1) % capacity;  // wrap around
        sz++;
    }

    // O(1)
    int dequeue() {
        if (isEmpty()) return -1;
        int val = arr[front];
        front = (front + 1) % capacity;  // wrap around
        sz--;
        return val;
    }

    int peek() { return isEmpty() ? -1 : arr[front]; }
    bool isEmpty() { return sz == 0; }
    bool isFull() { return sz == capacity; }
    int size() { return sz; }
};

int main() {
    CircularQueue q(5);
    q.enqueue(1); q.enqueue(2); q.enqueue(3);
    cout << q.dequeue() << endl;  // 1
    cout << q.dequeue() << endl;  // 2
    q.enqueue(4); q.enqueue(5); q.enqueue(6);
    // front crept to index 2, but rear wrapped — no wasted space
    cout << q.size() << endl;  // 4
    return 0;
}
```

The key line is `(rear + 1) % capacity` and `(front + 1) % capacity` — modulo arithmetic is what makes the array circular. The size variable is maintained separately to distinguish between full and empty, since both states have `front == rear` in a naive check.

---

## Complexity Summary

|Operation|Time|Space|
|---|---|---|
|Enqueue|$O(1)$|$O(1)$|
|Dequeue|$O(1)$|$O(1)$|
|Peek|$O(1)$|$O(1)$|
|Total space|—|$O(\text{capacity})$|

---

Here is `04_04_Deque_Double_Ended_Queue.md`:

---

# Deque — Double Ended Queue

A regular queue only allows insertion at the rear and deletion at the front. A **deque** (pronounced "deck") removes this restriction — you can insert and delete at _both_ ends.

> **Definition.** A _deque_ (double-ended queue) is a linear data structure that allows insertion and deletion at both the front and the rear in $O(1)$.

This makes the deque strictly more general than both a stack and a queue:

- Use only the back → behaves like a stack (push/pop at back)
- Use front insert and back delete (or vice versa) → behaves like a queue
- Use both ends freely → full deque

```cpp
#include <iostream>
#include <deque>
using namespace std;

int main() {
    deque<int> dq;

    dq.push_back(2);    // [2]
    dq.push_front(1);   // [1, 2]
    dq.push_back(3);    // [1, 2, 3]
    dq.push_front(0);   // [0, 1, 2, 3]

    cout << dq.front() << endl;  // 0
    cout << dq.back() << endl;   // 3

    dq.pop_front();  // [1, 2, 3]
    dq.pop_back();   // [1, 2]

    cout << dq.front() << endl;  // 1
    cout << dq.back() << endl;   // 2

    return 0;
}
```

---

## Manual Implementation — Using a Doubly Linked List

`std::deque` is internally complex (a segmented array). For exam purposes, a doubly linked list is the cleanest manual implementation — insertFront, insertBack, deleteFront, deleteBack are all $O(1)$ as we saw in [[02_02_Doubly_Linked_List]].

```cpp
// All four operations on a doubly linked list are O(1)
// See 02_02_Doubly_Linked_List for full implementation

dll.insertFront(x);  // push_front
dll.insertBack(x);   // push_back
dll.deleteFront();   // pop_front
dll.deleteBack();    // pop_back
```

---

## Complexity Summary

|Operation|Time|
|---|---|
|push_front|$O(1)$|
|push_back|$O(1)$|
|pop_front|$O(1)$|
|pop_back|$O(1)$|
|front / back|$O(1)$|

The deque is the underlying data structure used in the **Sliding Window Maximum** problem in the applications folder — its ability to efficiently add and remove from both ends is exactly what that problem exploits.

> 📖 For std::deque internals: [CPP Reference — std::deque](https://en.cppreference.com/w/cpp/container/deque)

---

Here is `04_05_01_Sliding_Window_Maximum.md`:

---

# Sliding Window Maximum

Given an array and a window size $k$, find the maximum element in every contiguous subarray of size $k$.

Example: $[1, 3, -1, -3, 5, 3, 6, 7]$, $k = 3$ → $[3, 3, 5, 5, 6, 7]$

The naive approach checks the maximum of every window — $O(nk)$. The deque-based approach does it in $O(n)$.

**The idea** — maintain a deque of indices such that:

1. The deque only contains indices within the current window
2. Elements in the deque are in decreasing order of their values

This means the front of the deque always holds the index of the maximum element in the current window. When a new element comes in, pop from the back all elements smaller than it — they can never be the maximum while the new element is in the window. When the window slides, pop from the front if the front index has left the window.

> **Definition.** The _sliding window maximum_ uses a monotonic deque (decreasing) to maintain the maximum of the current window, allowing $O(1)$ per step by discarding elements that can never be the window maximum.

```cpp
#include <iostream>
#include <vector>
#include <deque>
using namespace std;

// Time: O(n), Space: O(k)
vector<int> slidingWindowMax(vector<int>& arr, int k) {
    int n = arr.size();
    vector<int> result;
    deque<int> dq;  // stores indices, decreasing order of values

    for (int i = 0; i < n; i++) {
        // Remove indices outside the window
        while (!dq.empty() && dq.front() < i - k + 1)
            dq.pop_front();

        // Remove indices whose values are smaller than current
        // They can never be maximum while arr[i] is in window
        while (!dq.empty() && arr[dq.back()] < arr[i])
            dq.pop_back();

        dq.push_back(i);

        // Window is fully formed from index k-1 onwards
        if (i >= k - 1)
            result.push_back(arr[dq.front()]);
    }
    return result;
}

int main() {
    vector<int> arr = {1, 3, -1, -3, 5, 3, 6, 7};
    vector<int> res = slidingWindowMax(arr, 3);
    for (int x : res) cout << x << " ";  // 3 3 5 5 6 7
    return 0;
}
```

**Why $O(n)$** — each element is added to the deque once and removed at most once, so total deque operations across all $n$ steps is $2n = O(n)$. Same amortized argument as the monotonic stack.

**Complexity:** Time $O(n)$, Space $O(k)$ for the deque.

---

Here is `04_05_02_BFS_Queue_Usage.md`:

---

# BFS Queue Usage

**Breadth First Search** is the most algorithmically significant application of queues. It explores a graph (or tree) level by level — first all nodes at distance 1 from the source, then distance 2, and so on. A queue is what enforces this order naturally.

> **Definition.** _Breadth First Search_ is a graph traversal algorithm that explores all nodes at the current depth before moving to nodes at the next depth, using a queue to maintain the order of exploration.

This note covers BFS as a queue pattern. The full graph theory treatment — connected components, shortest paths, and BFS on various graph types — is in [[10_02_01_BFS_and_Connected_Components]].

---

## BFS on a Tree

```cpp
#include <iostream>
#include <queue>
using namespace std;

struct TreeNode {
    int val;
    TreeNode* left;
    TreeNode* right;
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
};

// Level order traversal — Time: O(n), Space: O(n)
void bfs(TreeNode* root) {
    if (!root) return;
    queue<TreeNode*> q;
    q.push(root);

    while (!q.empty()) {
        TreeNode* node = q.front(); q.pop();
        cout << node->val << " ";

        if (node->left)  q.push(node->left);
        if (node->right) q.push(node->right);
    }
}
```

---

## BFS on a Graph

```cpp
#include <iostream>
#include <vector>
#include <queue>
using namespace std;

// Time: O(V + E), Space: O(V)
void bfsGraph(int start, vector<vector<int>>& adj, int V) {
    vector<bool> visited(V, false);
    queue<int> q;

    visited[start] = true;
    q.push(start);

    while (!q.empty()) {
        int node = q.front(); q.pop();
        cout << node << " ";

        for (int neighbor : adj[node]) {
            if (!visited[neighbor]) {
                visited[neighbor] = true;
                q.push(neighbor);
            }
        }
    }
}
```

---

## Why a Queue — Not a Stack

The visited array prevents revisiting nodes. But the _order_ of traversal is determined entirely by the data structure:

|Data Structure|Traversal order|Algorithm|
|---|---|---|
|Queue (FIFO)|Level by level, nearest first|BFS|
|Stack (LIFO)|Deep before wide, one branch at a time|DFS|

If you replaced the queue with a stack in the BFS code above, you would get DFS. The queue is what guarantees that nodes closer to the source are processed before nodes farther away — which is exactly what makes BFS the right tool for **shortest path** in unweighted graphs.

**Complexity:** Time $O(V + E)$ — each vertex is enqueued once ($O(V)$) and each edge is examined once ($O(E)$). Space $O(V)$ for the queue and visited array.

> 📖 For full BFS theory and shortest paths: [[10_02_01_BFS_and_Connected_Components]] 📖 For BFS visualized: [Visualgo — BFS/DFS](https://visualgo.net/en/dfsbfs)

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

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

| Implementation |Naive Array|Linked List|std::queue|
| -------------- | ----------------- | ----------- | ---------------- |
| Enqueue        |$O(1)$|$O(1)$|$O(1)$ amortized|
| Dequeue        |$O(1)$|$O(1)$|$O(1)$|
| Space waste    |Yes — front creep|No|No|
| Size limit     |Fixed|Dynamic|Dynamic|
| Cache          |Good|Poor|Good|

> 📖 For std::queue internals: [CPP Reference — std::queue](https://en.cppreference.com/w/cpp/container/queue)

---

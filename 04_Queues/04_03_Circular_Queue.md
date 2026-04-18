
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

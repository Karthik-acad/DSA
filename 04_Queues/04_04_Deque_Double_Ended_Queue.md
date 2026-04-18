
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

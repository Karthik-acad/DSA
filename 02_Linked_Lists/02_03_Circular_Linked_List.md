

---

# Circular Linked List

Both singly and doubly linked lists have a clear start and end — head points to the first node, the last node points to `nullptr`. A **circular linked list** removes that hard boundary — the last node points back to the head, forming a circle.

> **Definition.** A _circular linked list_ is a linked list where the last node's `next` pointer points back to the head instead of `nullptr`, forming a closed loop. It can be singly or doubly circular.

Think of it like a round table — there is no first or last seat, just a continuous loop. This is useful whenever you need to cycle through elements repeatedly — round-robin scheduling, turn-based games, media playlists.

```cpp
#include <iostream>
using namespace std;

struct Node {
    int data;
    Node* next;
    Node(int val) : data(val), next(nullptr) {}
};

class CircularLinkedList {
public:
    Node* tail;  // We store tail, not head — head is just tail->next
    CircularLinkedList() : tail(nullptr) {}

    // Insert at front — O(1)
    void insertFront(int val) {
        Node* newNode = new Node(val);
        if (!tail) {
            tail = newNode;
            tail->next = tail;  // points to itself
            return;
        }
        newNode->next = tail->next;  // new node points to current head
        tail->next = newNode;        // tail points to new head
    }

    // Insert at back — O(1) since we maintain tail
    void insertBack(int val) {
        insertFront(val);   // insert at front
        tail = tail->next;  // then move tail forward — new node becomes tail
    }

    // Delete front — O(1)
    void deleteFront() {
        if (!tail) return;
        if (tail->next == tail) {  // only one node
            delete tail;
            tail = nullptr;
            return;
        }
        Node* temp = tail->next;   // head
        tail->next = temp->next;   // tail now points to new head
        delete temp;
    }

    // Print — O(n)
    void print() {
        if (!tail) return;
        Node* curr = tail->next;  // start from head
        do {
            cout << curr->data << " -> ";
            curr = curr->next;
        } while (curr != tail->next);
        cout << "(back to head)" << endl;
    }
};

int main() {
    CircularLinkedList list;
    list.insertBack(1);
    list.insertBack(2);
    list.insertBack(3);
    list.print();  // 1 -> 2 -> 3 -> (back to head)

    list.deleteFront();
    list.print();  // 2 -> 3 -> (back to head)
    return 0;
}
```

The key implementation choice — store `tail` instead of `head`. Since `tail->next` is always the head, you get $O(1)$ access to both ends. If you stored `head` instead, inserting at the back would cost $O(n)$.

---

## Complexity Summary

|Operation|Time|Space|
|---|---|---|
|Insert at front|$O(1)$|$O(1)$|
|Insert at back|$O(1)$|$O(1)$|
|Delete front|$O(1)$|$O(1)$|
|Search|$O(n)$|$O(1)$|
|Traverse|$O(n)$|$O(1)$|

The main thing to watch out for — traversal must use a `do-while` loop (or check against the starting node), not a `nullptr` check, since there is no `nullptr` in a circular list. An infinite loop is a common bug here.

> 📖 For applications in OS scheduling: [Operating Systems: Three Easy Pieces — Scheduling](https://pages.cs.wisc.edu/~remzi/OSTEP/cpu-sched.pdf)

---

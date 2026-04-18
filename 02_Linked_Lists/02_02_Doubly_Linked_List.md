
---

# Doubly Linked List

A singly linked list only knows how to go _forward_ — each node points to the next. This means if you are at some node and need the one before it, you have to start over from the head. Traversal is one-directional.

A **doubly linked list** fixes this by giving each node two pointers — one to the next node and one to the previous. You can now traverse in both directions, and deletion becomes cleaner since you never need to track the previous node separately.

> **Definition.** A _doubly linked list_ is a linked list where each node contains a data field, a pointer to the next node, and a pointer to the previous node. The list maintains both a `head` and a `tail` pointer.

The tradeoff — every node now stores an extra pointer, so memory usage per node increases. And every insert/delete must update two pointers instead of one, making the code slightly more involved but not more complex in terms of Big-O.

---

## Structure

```cpp
struct Node {
    int data;
    Node* next;
    Node* prev;
    Node(int val) : data(val), next(nullptr), prev(nullptr) {}
};
```

---

## Core Operations

```cpp
class DoublyLinkedList {
public:
    Node* head;
    Node* tail;
    DoublyLinkedList() : head(nullptr), tail(nullptr) {}

    // Insert at head — O(1)
    void insertFront(int val) {
        Node* newNode = new Node(val);
        if (!head) { head = tail = newNode; return; }
        newNode->next = head;
        head->prev = newNode;
        head = newNode;
    }

    // Insert at tail — O(1) since we maintain tail pointer
    void insertBack(int val) {
        Node* newNode = new Node(val);
        if (!tail) { head = tail = newNode; return; }
        tail->next = newNode;
        newNode->prev = tail;
        tail = newNode;
    }

    // Delete a given node — O(1) given the node
    // This is the key advantage over singly linked list
    void deleteNode(Node* node) {
        if (!node) return;
        if (node->prev) node->prev->next = node->next;
        else head = node->next;          // node was head
        if (node->next) node->next->prev = node->prev;
        else tail = node->prev;          // node was tail
        delete node;
    }

    // Print forward — O(n)
    void printForward() {
        Node* curr = head;
        while (curr) {
            cout << curr->data << " <-> ";
            curr = curr->next;
        }
        cout << "nullptr" << endl;
    }

    // Print backward — O(n)
    void printBackward() {
        Node* curr = tail;
        while (curr) {
            cout << curr->data << " <-> ";
            curr = curr->prev;
        }
        cout << "nullptr" << endl;
    }
};

int main() {
    DoublyLinkedList list;
    list.insertBack(1);
    list.insertBack(2);
    list.insertBack(3);
    list.insertFront(0);
    list.printForward();   // 0 <-> 1 <-> 2 <-> 3 <-> nullptr
    list.printBackward();  // 3 <-> 2 <-> 1 <-> 0 <-> nullptr

    list.deleteNode(list.head->next);  // delete node with value 1
    list.printForward();   // 0 <-> 2 <-> 3 <-> nullptr
    return 0;
}
```

Notice `deleteNode` takes the node directly and runs in $O(1)$ — in a singly linked list deleting a known node still required $O(n)$ to find its predecessor. Here the `prev` pointer gives us that for free.

---

## Complexity Summary

|Operation|Time|Space|
|---|---|---|
|Insert at head|$O(1)$|$O(1)$|
|Insert at tail|$O(1)$|$O(1)$|
|Delete given node|$O(1)$|$O(1)$|
|Search|$O(n)$|$O(1)$|
|Traverse forward/backward|$O(n)$|$O(1)$|

---

## Singly vs Doubly

| Type              |Singly|Doubly|
|---|---|---|
| Pointers per node |1|2|
| Memory per node   |Less|More|
| Delete given node |$O(n)$ — need prev|$O(1)$ — has prev|
| Insert at tail    |$O(n)$ without tail ptr|$O(1)$ with tail ptr|
| Reverse traversal |Not possible|$O(n)$|

Doubly linked lists are the basis for many practical data structures — the LRU Cache, the browser history back/forward, and the C++ `std::list` are all doubly linked lists under the hood.

> 📖 For std::list internals: [CPP Reference — std::list](https://en.cppreference.com/w/cpp/container/list)

---

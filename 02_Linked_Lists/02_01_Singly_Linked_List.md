
---

# Singly Linked List

From our study of arrays, we know they store elements in contiguous memory — and that this is both their strength and their weakness. Access is $O(1)$ but inserting or deleting in the middle costs $O(n)$ because everything has to shift. What if we had a structure where insertion and deletion were cheap, and we were okay with giving up $O(1)$ access?

That is exactly the tradeoff a **linked list** makes.

Instead of storing elements side by side in memory, a linked list stores each element in a separate **node** that contains two things — the data itself, and a pointer to the next node. The nodes can be anywhere in memory — they do not need to be adjacent. The pointers are what chain them together into a sequence.

> **Definition.** A _singly linked list_ is a linear data structure where each node contains a data field and a pointer to the next node in the sequence. The list is accessed via a pointer to the first node called the **head**. The last node points to `nullptr`.

Think of it like a treasure hunt — each clue tells you where the next clue is. You cannot skip to clue 7 directly, you must follow the chain from clue 1.

---

## Structure

```cpp
#include <iostream>
using namespace std;

struct Node {
    int data;
    Node* next;
    Node(int val) : data(val), next(nullptr) {}
};
```

A linked list is just a `head` pointer. If `head == nullptr` the list is empty.

---

## Core Operations

```cpp
class SinglyLinkedList {
public:
    Node* head;
    SinglyLinkedList() : head(nullptr) {}

    // Insert at head — O(1)
    void insertFront(int val) {
        Node* newNode = new Node(val);
        newNode->next = head;
        head = newNode;
    }

    // Insert at tail — O(n)
    void insertBack(int val) {
        Node* newNode = new Node(val);
        if (!head) { head = newNode; return; }
        Node* curr = head;
        while (curr->next)
            curr = curr->next;
        curr->next = newNode;
    }

    // Insert after a given node — O(1) given the node
    void insertAfter(Node* prev, int val) {
        if (!prev) return;
        Node* newNode = new Node(val);
        newNode->next = prev->next;
        prev->next = newNode;
    }

    // Delete by value — O(n)
    void deleteVal(int val) {
        if (!head) return;
        if (head->data == val) {
            Node* temp = head;
            head = head->next;
            delete temp;
            return;
        }
        Node* curr = head;
        while (curr->next && curr->next->data != val)
            curr = curr->next;
        if (curr->next) {
            Node* temp = curr->next;
            curr->next = temp->next;
            delete temp;
        }
    }

    // Search — O(n)
    Node* search(int val) {
        Node* curr = head;
        while (curr) {
            if (curr->data == val) return curr;
            curr = curr->next;
        }
        return nullptr;
    }

    // Print — O(n)
    void print() {
        Node* curr = head;
        while (curr) {
            cout << curr->data << " -> ";
            curr = curr->next;
        }
        cout << "nullptr" << endl;
    }

    // Length — O(n)
    int length() {
        int count = 0;
        Node* curr = head;
        while (curr) { count++; curr = curr->next; }
        return count;
    }

    // Reverse — O(n), O(1) space
    void reverse() {
        Node* prev = nullptr;
        Node* curr = head;
        while (curr) {
            Node* next = curr->next;
            curr->next = prev;
            prev = curr;
            curr = next;
        }
        head = prev;
    }
};

int main() {
    SinglyLinkedList list;
    list.insertBack(1);
    list.insertBack(2);
    list.insertBack(3);
    list.insertFront(0);
    list.print();    // 0 -> 1 -> 2 -> 3 -> nullptr

    list.deleteVal(2);
    list.print();    // 0 -> 1 -> 3 -> nullptr

    list.reverse();
    list.print();    // 3 -> 1 -> 0 -> nullptr

    return 0;
}
```

---

## Complexity Summary

|Operation|Time|Space|
|---|---|---|
|Insert at head|$O(1)$|$O(1)$|
|Insert at tail|$O(n)$|$O(1)$|
|Insert after node|$O(1)$ given node|$O(1)$|
|Delete by value|$O(n)$|$O(1)$|
|Search|$O(n)$|$O(1)$|
|Access by index|$O(n)$|$O(1)$|
|Reverse|$O(n)$|$O(1)$|

The fundamental difference from arrays — insertion and deletion at a known position are $O(1)$, but you have to _find_ that position first which costs $O(n)$. There is no shortcut to the middle of the chain.

---

## Arrays vs Singly Linked Lists

| Type              |Array|Singly Linked List|
|---|---|---|
| Memory layout     |Contiguous|Scattered|
| Access by index   |$O(1)$|$O(n)$|
| Insert at head    |$O(n)$ shift|$O(1)$|
| Insert at middle  |$O(n)$ shift|$O(1)$ given node|
| Cache performance |Excellent|Poor|
| Extra memory      |None|One pointer per node|

Cache performance deserves a mention — because array elements are contiguous, the CPU cache loads several of them at once. Linked list nodes are scattered in memory so every traversal step is potentially a cache miss. In practice this makes arrays faster than their complexity suggests for small $n$.

> 📖 For memory layout and cache effects: [What every programmer should know about memory — Ulrich Drepper](https://people.freebsd.org/~lstewart/articles/cpumemory.pdf)

---

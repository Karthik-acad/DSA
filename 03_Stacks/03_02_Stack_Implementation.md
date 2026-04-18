
---

# Stack Implementation

The Stack ADT says what operations we need — `push`, `pop`, `peek`, `isEmpty`. Now we look at how to actually build it. There are two natural implementations — using an array and using a linked list. A third option is to use C++'s built-in `std::stack`.

---

## Implementation 1 — Using a Static Array

The simplest approach. Maintain an array and an integer `top` that tracks the index of the topmost element. Push increments `top` and writes, pop reads and decrements.

```cpp
#include <iostream>
using namespace std;

class Stack {
    int arr[1000];
    int top;
public:
    Stack() : top(-1) {}

    // O(1)
    void push(int x) {
        if (top == 999) { cout << "Stack overflow" << endl; return; }
        arr[++top] = x;
    }

    // O(1)
    int pop() {
        if (isEmpty()) { cout << "Stack underflow" << endl; return -1; }
        return arr[top--];
    }

    // O(1)
    int peek() {
        if (isEmpty()) return -1;
        return arr[top];
    }

    // O(1)
    bool isEmpty() { return top == -1; }

    // O(1)
    int size() { return top + 1; }
};
```

Simple and cache-friendly, but the fixed size is a hard constraint — you must know the maximum size upfront.

---

## Implementation 2 — Using a Dynamic Array (vector)

Same idea but using `vector<int>` instead of a fixed array — automatically resizes as needed.

```cpp
#include <iostream>
#include <vector>
using namespace std;

class Stack {
    vector<int> arr;
public:
    // O(1) amortized
    void push(int x) { arr.push_back(x); }

    // O(1)
    int pop() {
        if (isEmpty()) return -1;
        int val = arr.back();
        arr.pop_back();
        return val;
    }

    // O(1)
    int peek() {
        if (isEmpty()) return -1;
        return arr.back();
    }

    bool isEmpty() { return arr.empty(); }
    int size() { return arr.size(); }
};
```

---

## Implementation 3 — Using a Linked List

Each push creates a new node at the head, each pop removes the head. No size limit, no amortized cost — every operation is strictly $O(1)$.

```cpp
#include <iostream>
using namespace std;

struct Node {
    int data;
    Node* next;
    Node(int val) : data(val), next(nullptr) {}
};

class Stack {
    Node* top;
    int sz;
public:
    Stack() : top(nullptr), sz(0) {}

    // O(1)
    void push(int x) {
        Node* newNode = new Node(x);
        newNode->next = top;
        top = newNode;
        sz++;
    }

    // O(1)
    int pop() {
        if (isEmpty()) return -1;
        int val = top->data;
        Node* temp = top;
        top = top->next;
        delete temp;
        sz--;
        return val;
    }

    // O(1)
    int peek() {
        if (isEmpty()) return -1;
        return top->data;
    }

    bool isEmpty() { return top == nullptr; }
    int size() { return sz; }
};
```

---

## Implementation 4 — Using std::stack

In practice you will use C++'s built-in stack. It is a container adaptor — by default backed by `std::deque` but you can specify `std::vector` or `std::list`.

```cpp
#include <iostream>
#include <stack>
using namespace std;

int main() {
    stack<int> st;

    st.push(1);
    st.push(2);
    st.push(3);

    cout << st.top() << endl;   // 3
    st.pop();
    cout << st.top() << endl;   // 2
    cout << st.size() << endl;  // 2
    cout << st.empty() << endl; // 0 (false)

    return 0;
}
```

There are more implementations possible — two stacks using one array, stack using a queue — but the four above cover everything you need. The **vector-based** implementation is what we will primarily use going forward since it combines simplicity with no fixed size constraint.

---

## Implementation Comparison

| Implementation  |Static Array|Dynamic Array|Linked List|std::stack|
|---|---|---|---|---|
| Push            |$O(1)$|$O(1)$ amortized|$O(1)$|$O(1)$ amortized|
| Pop             |$O(1)$|$O(1)$|$O(1)$|$O(1)$|
| Size limit      |Fixed|Dynamic|Dynamic|Dynamic|
| Cache friendly  |Yes|Yes|No|Depends|
| Memory overhead |Wasted if underfilled|Small|One pointer per node|Depends|

> 📖 For std::stack internals: [CPP Reference — std::stack](https://en.cppreference.com/w/cpp/container/stack)

---


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

Here is `02_03_Circular_Linked_List.md`:

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

Here is `02_04_Floyd_Cycle_Detection.md`:

---

# Floyd's Cycle Detection

Consider this problem — given a linked list, does it contain a cycle? A cycle means some node's `next` pointer points back to an earlier node, creating an infinite loop. If you just traverse naively, you loop forever.

One approach — store every visited node in a hash set and check if you visit the same node twice. That works in $O(n)$ time but uses $O(n)$ extra space.

**Floyd's cycle detection algorithm** solves this in $O(n)$ time and $O(1)$ space using two pointers — a slow one that moves one step at a time and a fast one that moves two steps at a time.

> **Definition.** _Floyd's cycle detection algorithm_ (also called the tortoise and hare algorithm) uses two pointers moving at different speeds through a sequence. If a cycle exists, the fast pointer will eventually lap the slow pointer and they will meet inside the cycle.

The intuition — imagine two runners on a circular track. One runs twice as fast as the other. No matter where they start, the faster one will eventually lap the slower one and they will be at the same position. If there is no cycle (the track has an end), the fast runner simply reaches the end first.

---

## Part 1 — Detect a Cycle

```cpp
// Time: O(n), Space: O(1)
bool hasCycle(Node* head) {
    Node* slow = head;
    Node* fast = head;

    while (fast && fast->next) {
        slow = slow->next;
        fast = fast->next->next;
        if (slow == fast) return true;  // cycle detected
    }
    return false;
}
```

---

## Part 2 — Find the Cycle Entry Point

Once we know a cycle exists, where does it begin? There is a beautiful mathematical result here.

Say the cycle starts at distance $\mu$ from the head, and the cycle length is $\lambda$. When slow and fast meet, slow has taken $k$ steps and fast has taken $2k$ steps. The extra $k$ steps fast took are laps around the cycle.

It turns out — if you reset one pointer to the head and leave the other at the meeting point, then advance both one step at a time, they meet exactly at the cycle entry point.

$$\text{distance from head to entry} = \text{distance from meeting point to entry}$$

```cpp
// Time: O(n), Space: O(1)
Node* cycleEntry(Node* head) {
    Node* slow = head;
    Node* fast = head;

    // Phase 1 — detect
    while (fast && fast->next) {
        slow = slow->next;
        fast = fast->next->next;
        if (slow == fast) break;
    }

    if (!fast || !fast->next) return nullptr;  // no cycle

    // Phase 2 — find entry
    slow = head;
    while (slow != fast) {
        slow = slow->next;
        fast = fast->next;
    }
    return slow;  // entry point
}
```

---

## Part 3 — Find Cycle Length

Once you have the entry point, just count how many steps it takes to return to it:

```cpp
int cycleLength(Node* entry) {
    if (!entry) return 0;
    int length = 1;
    Node* curr = entry->next;
    while (curr != entry) {
        length++;
        curr = curr->next;
    }
    return length;
}
```

---

## Complexity Summary

|Operation|Time|Space|
|---|---|---|
|Detect cycle|$O(n)$|$O(1)$|
|Find entry point|$O(n)$|$O(1)$|
|Find cycle length|$O(\lambda)$|$O(1)$|

> 📖 For the mathematical proof of why the two-pointer trick finds the entry: [CP Algorithms — Floyd's Algorithm](https://cp-algorithms.com/others/tortoise_and_hare.html)

---

Here is `02_05_Runner_Technique.md`:

---

# Runner Technique

The **runner technique** is a general linked list pattern where you use two pointers moving at different speeds or with a fixed gap between them to solve problems that would otherwise require two passes or extra space.

It is closely related to Floyd's algorithm but more general — Floyd specifically detects cycles, while the runner technique is a broader strategy applied to problems like finding the middle, finding the $k$-th from last, or checking palindromes.

> **Definition.** The _runner technique_ uses two pointers traversing a linked list simultaneously — either at different speeds (fast/slow) or with a fixed offset — to gather information about the list structure in a single pass.

---

## Common Applications

**Find the middle node** — fast pointer moves two steps, slow moves one. When fast reaches the end, slow is at the middle.

```cpp
// Time: O(n), Space: O(1)
Node* findMiddle(Node* head) {
    Node* slow = head;
    Node* fast = head;
    while (fast && fast->next) {
        slow = slow->next;
        fast = fast->next->next;
    }
    return slow;  // middle node
}
```

**Find k-th node from end** — advance fast pointer $k$ steps ahead, then move both one step at a time. When fast reaches the end, slow is at the $k$-th from last.

```cpp
// Time: O(n), Space: O(1)
Node* kthFromEnd(Node* head, int k) {
    Node* slow = head;
    Node* fast = head;

    // Advance fast k steps
    for (int i = 0; i < k; i++) {
        if (!fast) return nullptr;  // k > length
        fast = fast->next;
    }

    // Move both until fast reaches end
    while (fast) {
        slow = slow->next;
        fast = fast->next;
    }
    return slow;
}
```

**Check if linked list is palindrome** — find middle, reverse second half, compare with first half.

```cpp
// Time: O(n), Space: O(1)
bool isPalindrome(Node* head) {
    if (!head || !head->next) return true;

    // Find middle
    Node* slow = head;
    Node* fast = head;
    while (fast->next && fast->next->next) {
        slow = slow->next;
        fast = fast->next->next;
    }

    // Reverse second half
    Node* prev = nullptr;
    Node* curr = slow->next;
    while (curr) {
        Node* next = curr->next;
        curr->next = prev;
        prev = curr;
        curr = next;
    }

    // Compare
    Node* left = head;
    Node* right = prev;
    while (right) {
        if (left->data != right->data) return false;
        left = left->next;
        right = right->next;
    }
    return true;
}
```

---

## Complexity Summary

|Problem|Time|Space|
|---|---|---|
|Find middle|$O(n)$|$O(1)$|
|Find k-th from end|$O(n)$|$O(1)$|
|Check palindrome|$O(n)$|$O(1)$|

The runner technique is a mindset as much as an algorithm — whenever you see a linked list problem that seems to need two passes or extra storage, ask yourself if two pointers at different speeds can get you the same information in one pass.

> 📖 For more runner technique problems: [Cracking the Coding Interview — Linked Lists Chapter](http://www.crackingthecodinginterview.com/)

---

Here is `02_QQ_Linked_Lists_Quiz.md`:

---

## Quiz — Linked Lists

**Q1 — Identify the Complexity**

```cpp
Node* curr = head;
while (curr->next->next)
    curr = curr->next;
```

What does this do and what is its complexity?

- [ ] Finds the last node — $O(n)$
- [ ] Finds the second to last node — $O(n)$
- [ ] Finds the middle node — $O(n)$
- [ ] $O(1)$

> [!answer]- Answer Finds the second to last node in O(n). The condition curr->next->next checks that there are at least two more nodes ahead — so when the loop exits, curr->next is the last node and curr is second to last. Useful for deleting the last node without a tail pointer.

---

**Q2 — Trace the Output**

```cpp
SinglyLinkedList list;
list.insertBack(1);
list.insertBack(2);
list.insertBack(3);
list.insertFront(0);
list.deleteVal(2);
list.reverse();
list.print();
```

> [!answer]- Answer Step by step: after insertBack x3: 1->2->3. After insertFront: 0->1->2->3. After deleteVal(2): 0->1->3. After reverse: 3->1->0->nullptr.

---

**Q3 — True or False** Deleting a node from a doubly linked list given only a pointer to that node is $O(1)$, but deleting from a singly linked list given only a pointer to that node is $O(n)$.

- [ ] True
- [ ] False

> [!answer]- Answer True. In a doubly linked list, node->prev and node->next give you everything needed to stitch the neighbors together in O(1). In a singly linked list you need the predecessor to update its next pointer, and finding it requires traversing from head in O(n). There is a trick to delete a singly linked list node in O(1) by copying the next node's data into the current node and deleting next — but this fails for the last node.

---

**Q4 — Spot the Bug**

```cpp
bool hasCycle(Node* head) {
    Node* slow = head;
    Node* fast = head->next;
    while (slow != fast) {
        slow = slow->next;
        fast = fast->next->next;
    }
    return true;
}
```

> [!answer]- Answer Two bugs. First — no null check before fast->next->next, so if the list has no cycle this will crash with a null pointer dereference when fast reaches the end. Second — the function always returns true, it never returns false for the no-cycle case. The while loop either crashes or loops forever if there is no cycle. The correct approach checks fast && fast->next before dereferencing and returns false when that condition fails.

---

**Q5 — Apply Floyd's Algorithm** List: 1 -> 2 -> 3 -> 4 -> 5 -> 3 (node with value 3 again, cycle back). Trace Floyd's algorithm step by step. Where do slow and fast meet? What is the cycle entry point?

> [!answer]- Answer Nodes in order: head->1->2->3->4->5->back to 3. Trace: start: slow=1, fast=1. Step 1: slow=2, fast=3. Step 2: slow=3, fast=5. Step 3: slow=4, fast=4. They meet at node 4. Phase 2: reset slow to head=1, fast stays at 4. Step 1: slow=2, fast=5. Step 2: slow=3, fast=3. They meet at node 3 — the cycle entry point. Distance from head to entry = 2, distance from meeting point to entry = 2. Equal, as the theorem predicts.

---

**Q6 — Write it Yourself** Given a singly linked list, find the $k$-th node from the end in a single pass without knowing the length. Then find the middle node in a single pass.

> [!answer]- Answer k-th from end — runner technique with fixed gap:
> 
> ```cpp
> Node* kthFromEnd(Node* head, int k) {
>     Node* slow = head, *fast = head;
>     for (int i = 0; i < k; i++) fast = fast->next;
>     while (fast) { slow = slow->next; fast = fast->next; }
>     return slow;
> }
> ```
> 
> Middle node — fast/slow pointers:
> 
> ```cpp
> Node* middle(Node* head) {
>     Node* slow = head, *fast = head;
>     while (fast && fast->next) {
>         slow = slow->next;
>         fast = fast->next->next;
>     }
>     return slow;
> }
> ```
> 
> Both are O(n) time, O(1) space, single pass.

---

**Q7 — Identify the Complexity** You have a doubly linked list and you want to implement a stack using it. What is the complexity of push and pop?

- [ ] Push $O(1)$, Pop $O(1)$
- [ ] Push $O(1)$, Pop $O(n)$
- [ ] Push $O(n)$, Pop $O(1)$

> [!answer]- Answer Push O(1) and Pop O(1). Insert at head is O(1) for push, delete head is O(1) for pop — the doubly linked list gives you both for free. This is actually how std::stack works internally when backed by std::list.

---

**Q8 — Write it Yourself** Reverse a singly linked list iteratively. Then think — can you do it recursively? What is the space complexity of the recursive version and why?

> [!answer]- Answer Iterative — O(n) time, O(1) space:
> 
> ```cpp
> Node* reverse(Node* head) {
>     Node* prev = nullptr, *curr = head;
>     while (curr) {
>         Node* next = curr->next;
>         curr->next = prev;
>         prev = curr;
>         curr = next;
>     }
>     return prev;
> }
> ```
> 
> Recursive — O(n) time, O(n) space:
> 
> ```cpp
> Node* reverse(Node* head) {
>     if (!head || !head->next) return head;
>     Node* newHead = reverse(head->next);
>     head->next->next = head;
>     head->next = nullptr;
>     return newHead;
> }
> ```
> 
> The recursive version uses O(n) stack space because n call frames are alive simultaneously waiting for the base case. The iterative version is always preferred in practice for this reason.

---

And finally `02_00_Linked_Lists.md`:

---

# Linked Lists

## Notes

- [[02_01_Singly_Linked_List]]
- [[02_02_Doubly_Linked_List]]
- [[02_03_Circular_Linked_List]]
- [[02_04_Floyd_Cycle_Detection]]
- [[02_05_Runner_Technique]]

## Quiz

- [[02_QQ_Linked_Lists_Quiz]]

---

## Quick Reference

### Node Structure

|Type|Pointers per node|Extra memory|
|---|---|---|
|Singly|`next`|1 pointer|
|Doubly|`next`, `prev`|2 pointers|
|Circular|`next` (points to head)|1 pointer|

---

### Complexity Comparison

|Operation|Singly|Doubly|Array|
|---|---|---|---|
|Access by index|$O(n)$|$O(n)$|$O(1)$|
|Insert at head|$O(1)$|$O(1)$|$O(n)$|
|Insert at tail|$O(n)$ / $O(1)$*|$O(1)$*|$O(1)$ amortized|
|Insert at middle|$O(1)$ given node|$O(1)$ given node|$O(n)$|
|Delete given node|$O(n)$ need prev|$O(1)$|$O(n)$|
|Search|$O(n)$|$O(n)$|$O(n)$|

*$O(1)$ if tail pointer is maintained

---

### Floyd's Cycle Detection

Two pointers — slow moves 1 step, fast moves 2 steps:

|Phase|Action|Result|
|---|---|---|
|Phase 1|Move until slow == fast|Detect cycle|
|Phase 2|Reset slow to head, move both 1 step|Find cycle entry|

$$\text{distance(head → entry)} = \text{distance(meeting point → entry)}$$

---

### Runner Technique Patterns

|Problem|Slow|Fast|Gap|
|---|---|---|---|
|Find middle|1 step|2 steps|—|
|k-th from end|1 step|1 step|k ahead|
|Cycle detection|1 step|2 steps|—|

---
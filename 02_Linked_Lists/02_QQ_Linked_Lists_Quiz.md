
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

> [!answer]- 
> Answer Finds the second to last node in O(n). The condition curr->next->next checks that there are at least two more nodes ahead — so when the loop exits, curr->next is the last node and curr is second to last. Useful for deleting the last node without a tail pointer.

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

> [!answer]- 
> Answer Step by step: after insertBack x3: 1->2->3. After insertFront: 0->1->2->3. After deleteVal(2): 0->1->3. After reverse: 3->1->0->nullptr.

---

**Q3 — True or False** Deleting a node from a doubly linked list given only a pointer to that node is $O(1)$, but deleting from a singly linked list given only a pointer to that node is $O(n)$.

- [ ] True
- [ ] False

> [!answer]- 
> Answer True. In a doubly linked list, node->prev and node->next give you everything needed to stitch the neighbors together in O(1). In a singly linked list you need the predecessor to update its next pointer, and finding it requires traversing from head in O(n). There is a trick to delete a singly linked list node in O(1) by copying the next node's data into the current node and deleting next — but this fails for the last node.

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

> [!answer]- 
> Answer Two bugs. First — no null check before fast->next->next, so if the list has no cycle this will crash with a null pointer dereference when fast reaches the end. Second — the function always returns true, it never returns false for the no-cycle case. The while loop either crashes or loops forever if there is no cycle. The correct approach checks fast && fast->next before dereferencing and returns false when that condition fails.

---

**Q5 — Apply Floyd's Algorithm** List: 1 -> 2 -> 3 -> 4 -> 5 -> 3 (node with value 3 again, cycle back). Trace Floyd's algorithm step by step. Where do slow and fast meet? What is the cycle entry point?

> [!answer]- 
> Answer Nodes in order: head->1->2->3->4->5->back to 3. Trace: start: slow=1, fast=1. Step 1: slow=2, fast=3. Step 2: slow=3, fast=5. Step 3: slow=4, fast=4. They meet at node 4. Phase 2: reset slow to head=1, fast stays at 4. Step 1: slow=2, fast=5. Step 2: slow=3, fast=3. They meet at node 3 — the cycle entry point. Distance from head to entry = 2, distance from meeting point to entry = 2. Equal, as the theorem predicts.

---

**Q6 — Write it Yourself** Given a singly linked list, find the $k$-th node from the end in a single pass without knowing the length. Then find the middle node in a single pass.

> [!answer]- 
> Answer k-th from end — runner technique with fixed gap:
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

> [!answer]- 
> Answer Push O(1) and Pop O(1). Insert at head is O(1) for push, delete head is O(1) for pop — the doubly linked list gives you both for free. This is actually how std::stack works internally when backed by std::list.

---

**Q8 — Write it Yourself** Reverse a singly linked list iteratively. Then think — can you do it recursively? What is the space complexity of the recursive version and why?

> [!answer]- 
> Answer Iterative — O(n) time, O(1) space:
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


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


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

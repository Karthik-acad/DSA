
---

# BST Introduction

A plain binary tree has no rules about where values go — the left child could be larger or smaller than the parent. A **Binary Search Tree** adds one rule that unlocks efficient search.

> **Definition.** A _Binary Search Tree_ is a binary tree where for every node $v$:
> 
> - All values in $v$'s **left subtree** are **strictly less** than $v$'s value
> - All values in $v$'s **right subtree** are **strictly greater** than $v$'s value
> - Both subtrees are themselves BSTs

This single invariant is what gives BSTs their power. To search for a value, you compare with the current node and go left or right — eliminating half the remaining tree at each step. It is binary search applied to a tree structure.

```
        8
       / \
      3   10
     / \    \
    1   6    14
       / \   /
      4   7 13
```

This is a valid BST. Verify: every value in the left subtree of 8 is less than 8, every value in the right subtree is greater.

---

## The BST Property and Inorder Traversal

> **Theorem.** Inorder traversal of a BST always yields values in **sorted ascending order**.

For the tree above: 1, 3, 4, 6, 7, 8, 10, 13, 14 — perfectly sorted. This is not a coincidence — it follows directly from the BST property. The left subtree contains all smaller elements, so they are visited first, then the current node, then the larger right subtree.

This property is used constantly — it converts a BST into a sorted sequence in $O(n)$.

---

## Average vs Worst Case

The efficiency of BST operations depends entirely on the **height** of the tree:

|Tree shape|Height|Search / Insert / Delete|
|---|---|---|
|Balanced|$O(\log n)$|$O(\log n)$|
|Completely skewed|$O(n)$|$O(n)$|

A skewed BST (inserting sorted data into a plain BST) degenerates into a linked list:

```
1
 \
  2
   \
    3
     \
      4
```

This is why balanced BSTs (AVL, Red-Black) exist — they maintain $O(\log n)$ height by rebalancing on insert and delete.

---

## C++ STL

`std::set` and `std::map` are both backed by red-black trees — balanced BSTs. They guarantee $O(\log n)$ for all operations and maintain sorted order.

```cpp
#include <set>
#include <map>
using namespace std;

set<int> s;
s.insert(5); s.insert(3); s.insert(7);
for (int x : s) cout << x << " ";  // 3 5 7 — sorted

map<string, int> m;
m["b"] = 2; m["a"] = 1;
for (auto& [k,v] : m) cout << k << " ";  // a b — sorted by key
```

> 📖 For BST theory: [MIT 6.006 Lecture Notes — BSTs](https://ocw.mit.edu/courses/6-006-introduction-to-algorithms-spring-2020/pages/lecture-notes/)

---

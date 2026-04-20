
---

# Dictionary ADT

From our study of arrays and linked lists, accessing an element always required either knowing its index or scanning through the structure. But many real problems do not work with indices — they work with _keys_. You want to look up a student by their roll number, a word by its spelling, a user by their username. The natural question is — can we build a structure that maps keys to values and retrieves them efficiently?

This is exactly what a **dictionary** does.

> **Definition.** A _dictionary_ (also called a _map_ or _associative array_) is an Abstract Data Type that stores a collection of key-value pairs where each key is unique, and supports efficient insert, delete, and lookup by key.

Think of it like an actual dictionary — you look up a word (key) and get its definition (value). You never scan every page sequentially — you jump directly to the right section.

|Operation|Description|Ideal Complexity|
|---|---|---|
|`insert(key, value)`|Add a key-value pair|$O(1)$ or $O(\log n)$|
|`delete(key)`|Remove a key-value pair|$O(1)$ or $O(\log n)$|
|`search(key)`|Return value for given key|$O(1)$ or $O(\log n)$|
|`contains(key)`|Check if key exists|$O(1)$ or $O(\log n)$|

The ideal complexity depends on the implementation. We will look at three main implementations in this chapter — skip lists, hash tables, and later BSTs (in [[07_02_00_Binary_Search_Trees]]).

---

## The Key-Value Abstraction

The power of the dictionary ADT is that the _key_ can be anything hashable or comparable — an integer, a string, a tuple. The _value_ can be anything at all. This generality is why dictionaries appear everywhere:

- **Compiler symbol table** — variable name (key) → memory address (value)
- **DNS lookup** — domain name (key) → IP address (value)
- **Caching** — request URL (key) → cached response (value)
- **Graph adjacency** — node (key) → list of neighbors (value)
- **Word frequency** — word (key) → count (value)

---

## Implementations Overview

|Implementation|Search|Insert|Delete|Ordered?|
|---|---|---|---|---|
|Unsorted array|$O(n)$|$O(1)$|$O(n)$|No|
|Sorted array|$O(\log n)$|$O(n)$|$O(n)$|Yes|
|BST (balanced)|$O(\log n)$|$O(\log n)$|$O(\log n)$|Yes|
|Skip List|$O(\log n)$ avg|$O(\log n)$ avg|$O(\log n)$ avg|Yes|
|Hash Table|$O(1)$ avg|$O(1)$ avg|$O(1)$ avg|No|

Hash tables win on raw speed but lose ordering. BSTs and skip lists maintain sorted order at the cost of a log factor. The right choice depends on whether you need ordered traversal or just fast lookup.

In C++:

- `std::unordered_map` → hash table, $O(1)$ average
- `std::map` → red-black tree, $O(\log n)$ guaranteed, ordered

```cpp
#include <iostream>
#include <unordered_map>
#include <map>
using namespace std;

int main() {
    // Hash table — O(1) average, unordered
    unordered_map<string, int> freq;
    freq["apple"] = 3;
    freq["banana"] = 1;
    freq["apple"]++;
    cout << freq["apple"] << endl;  // 4

    // BST-backed map — O(log n), ordered
    map<string, int> ordered;
    ordered["banana"] = 1;
    ordered["apple"] = 3;
    for (auto& [k, v] : ordered)
        cout << k << ": " << v << endl;  // alphabetical order
    return 0;
}
```

> 📖 For dictionary ADT theory: [MIT 6.006 Lecture Notes — Hashing](https://ocw.mit.edu/courses/6-006-introduction-to-algorithms-spring-2020/pages/lecture-notes/)

---


---

# Separate Chaining

No matter how good the hash function, two different keys will sometimes map to the same index — this is a **collision**. The birthday paradox tells us why this is inevitable: with $m$ slots and $n$ keys, collisions are expected once $n \approx \sqrt{m}$. With $n > m$, collisions are guaranteed by the pigeonhole principle.

**Separate chaining** resolves collisions by letting each slot hold a linked list of all keys that hash to that slot.

> **Definition.** _Separate chaining_ is a collision resolution technique where each slot in the hash table contains a linked list (or other data structure) of all key-value pairs that hash to that slot.

```cpp
#include <iostream>
#include <vector>
#include <list>
#include <string>
using namespace std;

class HashTableChaining {
    int m;  // table size
    vector<list<pair<int,int>>> table;

    int hash(int key) { return key % m; }

public:
    HashTableChaining(int size) : m(size), table(size) {}

    // O(1) average, O(n) worst
    void insert(int key, int value) {
        int idx = hash(key);
        // Check if key exists and update
        for (auto& [k, v] : table[idx]) {
            if (k == key) { v = value; return; }
        }
        table[idx].push_back({key, value});
    }

    // O(1) average, O(n) worst
    int search(int key) {
        int idx = hash(key);
        for (auto& [k, v] : table[idx])
            if (k == key) return v;
        return -1;  // not found
    }

    // O(1) average, O(n) worst
    void remove(int key) {
        int idx = hash(key);
        table[idx].remove_if([key](pair<int,int>& p) {
            return p.first == key;
        });
    }

    void print() {
        for (int i = 0; i < m; i++) {
            cout << i << ": ";
            for (auto& [k, v] : table[i])
                cout << "(" << k << "," << v << ") ";
            cout << endl;
        }
    }
};

int main() {
    HashTableChaining ht(7);
    ht.insert(10, 100);
    ht.insert(20, 200);
    ht.insert(17, 170);  // 17 % 7 = 3, same as 10 % 7 = 3
    ht.insert(3,  30);   // 3 % 7 = 3 — three-way collision
    ht.print();
    cout << ht.search(17) << endl;  // 170
    ht.remove(17);
    cout << ht.search(17) << endl;  // -1
    return 0;
}
```

---

## Analysis

With a good hash function and load factor $\alpha = n/m$:

- **Expected chain length** = $\alpha$
- **Expected search time** = $O(1 + \alpha)$
- If $m = \Theta(n)$, then $\alpha = O(1)$ → $O(1)$ average

|Operation|Average|Worst Case|
|---|---|---|
|Insert|$O(1)$|$O(n)$ — all keys same slot|
|Search|$O(1 + \alpha)$|$O(n)$|
|Delete|$O(1 + \alpha)$|$O(n)$|

The worst case happens when all $n$ keys hash to the same slot — one chain of length $n$. A good hash function makes this astronomically unlikely.

> 📖 For average case proof: [CLRS Chapter 11.2](https://mitpress.mit.edu/9780262046305/introduction-to-algorithms/)

---

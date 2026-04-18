Starting with `06_01_Dictionary_ADT.md`:

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

Here is `06_02_Skip_Lists.md`:

---

# Skip Lists

Before hash tables became dominant, **skip lists** were a serious contender for dictionary implementations. They are elegant — a probabilistic data structure built on top of linked lists that achieves $O(\log n)$ average search, insert, and delete without the complexity of balanced trees.

> **Definition.** A _skip list_ is a layered linked list where each layer is a sparser version of the layer below. The bottom layer contains all elements in sorted order. Each higher layer contains a random subset of the elements below it, acting as an express lane for faster traversal.

Think of it like an express train system. The local train stops at every station (bottom layer). The express train skips most stations and only stops at major ones (higher layers). To reach your destination, you ride the express as far as possible, then switch to the local for the final stretch.

---

## Structure

A skip list with $n$ elements has:

- **Level 0** — all $n$ elements in sorted order (the full linked list)
- **Level 1** — approximately $n/2$ elements (every other node promoted)
- **Level 2** — approximately $n/4$ elements
- **Level $k$** — approximately $n/2^k$ elements
- **Maximum levels** — $O(\log n)$ expected

Each node is promoted to the next level with probability $p = 0.5$ (coin flip). This randomness is what gives skip lists their probabilistic guarantees.

```cpp
#include <iostream>
#include <vector>
#include <cstdlib>
#include <ctime>
#include <climits>
using namespace std;

const int MAX_LEVEL = 16;
const float P = 0.5;

struct SkipNode {
    int key;
    vector<SkipNode*> forward;  // forward[i] = next node at level i
    SkipNode(int k, int level) : key(k), forward(level + 1, nullptr) {}
};

class SkipList {
    SkipNode* header;
    int level;  // current maximum level

    int randomLevel() {
        int lvl = 0;
        while ((float)rand()/RAND_MAX < P && lvl < MAX_LEVEL)
            lvl++;
        return lvl;
    }

public:
    SkipList() : level(0) {
        header = new SkipNode(INT_MIN, MAX_LEVEL);
        srand(time(0));
    }

    // Search — O(log n) average
    bool search(int key) {
        SkipNode* curr = header;
        for (int i = level; i >= 0; i--) {
            while (curr->forward[i] && curr->forward[i]->key < key)
                curr = curr->forward[i];
        }
        curr = curr->forward[0];
        return curr && curr->key == key;
    }

    // Insert — O(log n) average
    void insert(int key) {
        vector<SkipNode*> update(MAX_LEVEL + 1);
        SkipNode* curr = header;

        // Find update positions at each level
        for (int i = level; i >= 0; i--) {
            while (curr->forward[i] && curr->forward[i]->key < key)
                curr = curr->forward[i];
            update[i] = curr;
        }

        int newLevel = randomLevel();
        if (newLevel > level) {
            for (int i = level + 1; i <= newLevel; i++)
                update[i] = header;
            level = newLevel;
        }

        SkipNode* newNode = new SkipNode(key, newLevel);
        for (int i = 0; i <= newLevel; i++) {
            newNode->forward[i] = update[i]->forward[i];
            update[i]->forward[i] = newNode;
        }
    }

    // Delete — O(log n) average
    void remove(int key) {
        vector<SkipNode*> update(MAX_LEVEL + 1);
        SkipNode* curr = header;

        for (int i = level; i >= 0; i--) {
            while (curr->forward[i] && curr->forward[i]->key < key)
                curr = curr->forward[i];
            update[i] = curr;
        }

        curr = curr->forward[0];
        if (!curr || curr->key != key) return;

        for (int i = 0; i <= level; i++) {
            if (update[i]->forward[i] != curr) break;
            update[i]->forward[i] = curr->forward[i];
        }
        delete curr;
    }

    void print() {
        for (int i = level; i >= 0; i--) {
            SkipNode* curr = header->forward[i];
            cout << "Level " << i << ": ";
            while (curr) { cout << curr->key << " "; curr = curr->forward[i]; }
            cout << endl;
        }
    }
};

int main() {
    SkipList sl;
    sl.insert(3); sl.insert(6); sl.insert(7);
    sl.insert(9); sl.insert(12); sl.insert(19);
    sl.print();
    cout << sl.search(6) << endl;   // 1 (true)
    cout << sl.search(15) << endl;  // 0 (false)
    sl.remove(6);
    cout << sl.search(6) << endl;   // 0 (false)
    return 0;
}
```

---

## Complexity Summary

|Operation|Average|Worst Case|
|---|---|---|
|Search|$O(\log n)$|$O(n)$|
|Insert|$O(\log n)$|$O(n)$|
|Delete|$O(\log n)$|$O(n)$|
|Space|$O(n)$ expected|$O(n \log n)$|

The worst case is theoretically $O(n)$ — if every coin flip promotes every element to every level (extremely unlikely). In practice with $p=0.5$, the expected number of comparisons for a search is $2\log n$.

---

## Skip List vs Balanced BST

||Skip List|Balanced BST (AVL/RB)|
|---|---|---|
|Search|$O(\log n)$ avg|$O(\log n)$ guaranteed|
|Insert|$O(\log n)$ avg|$O(\log n)$ guaranteed|
|Implementation|Simpler|Complex rotations|
|Ordering|Yes|Yes|
|Concurrent access|Easier to make thread-safe|Harder|
|Cache|Poor (pointers)|Poor (pointers)|

Skip lists were popularized by William Pugh in 1990. Redis uses skip lists internally for its sorted sets — a real production system choosing skip lists over balanced BSTs precisely because concurrent modification is simpler.

> 📖 Original paper: [Skip Lists — William Pugh, 1990](https://15721.courses.cs.cmu.edu/spring2018/papers/08-oltpindexes1/pugh-skiplists-cacm1990.pdf)

---

Now the Hash Tables subfolder. Here is `06_03_01_Hash_Function_Design.md`:

---

# Hash Function Design

The core idea of a hash table is deceptively simple — given a key, compute an index into an array and store the value there. That computation is done by a **hash function**.

> **Definition.** A _hash function_ $h$ maps a key from a universe $U$ to an integer index in the range $[0, m-1]$ where $m$ is the table size: $$h: U \rightarrow {0, 1, \ldots, m-1}$$

The quality of the hash function determines the quality of the hash table. A perfect hash function distributes keys uniformly — every slot gets roughly the same number of keys. A bad hash function clusters keys into a few slots, degrading performance to $O(n)$.

---

## Properties of a Good Hash Function

A good hash function must be:

- **Deterministic** — same key always produces same index
- **Uniform** — keys spread evenly across all slots
- **Fast** — $O(1)$ to compute
- **Avalanche effect** — small change in key causes large change in hash

---

## Common Hash Functions

**Division method** — simplest approach: $$h(k) = k \mod m$$

Choose $m$ to be a prime not close to a power of 2 — this avoids patterns in the key distribution landing in the same slots.

**Multiplication method** — more uniform: $$h(k) = \lfloor m \cdot (kA \mod 1) \rfloor$$

where $A$ is a constant, $0 < A < 1$. Knuth suggests $A = (\sqrt{5}-1)/2 \approx 0.618$.

**String hashing — Polynomial rolling hash:** $$h(s) = \left(\sum_{i=0}^{n-1} s[i] \cdot p^i\right) \mod m$$

where $p$ is a small prime (31 or 37 for lowercase letters) and $m$ is a large prime ($10^9 + 7$).

```cpp
#include <iostream>
#include <string>
using namespace std;

// Division method
int divisionHash(int key, int m) {
    return key % m;
}

// Multiplication method (Knuth's constant)
int multiplicationHash(int key, int m) {
    double A = 0.6180339887;  // (sqrt(5)-1)/2
    double val = key * A;
    val = val - (int)val;     // fractional part
    return (int)(m * val);
}

// Polynomial rolling hash for strings
// Time: O(|s|), good distribution for strings
long long stringHash(const string& s, int m) {
    long long hash = 0;
    long long p = 31, pPow = 1;
    for (char c : s) {
        hash = (hash + (c - 'a' + 1) * pPow) % m;
        pPow = (pPow * p) % m;
    }
    return hash;
}

int main() {
    int m = 13;  // prime table size
    cout << divisionHash(123, m) << endl;         // 123 % 13 = 7
    cout << multiplicationHash(123, m) << endl;
    cout << stringHash("hello", 1e9+7) << endl;
    return 0;
}
```

---

## What Makes a Bad Hash Function

```cpp
// BAD — maps all even keys to even slots, all odd to odd
// Clusters badly for common integer distributions
int badHash(int key, int m) { return key % m; }  // m = 16 (power of 2)

// BAD — for strings, just summing ASCII values
// "abc" and "bca" get the same hash — order ignored
int badStringHash(const string& s, int m) {
    int sum = 0;
    for (char c : s) sum += c;
    return sum % m;
}
```

---

## Load Factor

> **Definition.** The _load factor_ $\alpha$ of a hash table is: $$\alpha = \frac{n}{m}$$ where $n$ is the number of stored elements and $m$ is the table size.

The load factor directly determines performance:

- $\alpha < 0.7$ — good performance
- $\alpha \approx 1$ — collisions frequent, performance degrades
- $\alpha > 1$ — only possible with chaining, many collisions

When $\alpha$ exceeds a threshold (typically 0.75), the table is **rehashed** — a new table of double the size is allocated and all elements are reinserted. This is $O(n)$ but happens rarely enough to keep amortized insert at $O(1)$.

> 📖 For hash function theory: [MIT 6.006 Lecture 8 — Hashing](https://ocw.mit.edu/courses/6-006-introduction-to-algorithms-spring-2020/pages/lecture-notes/)

---

Here is `06_03_02_Separate_Chaining.md`:

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

Here is `06_03_03_Open_Addressing.md`:

---

# Open Addressing

Separate chaining stores colliding elements outside the table in linked lists. **Open addressing** takes a different approach — all elements live _inside_ the table itself. When a collision occurs, we probe other slots in the table until an empty one is found.

> **Definition.** _Open addressing_ is a collision resolution technique where all key-value pairs are stored within the hash table array. On collision, a _probe sequence_ determines the next slot to try until an empty slot is found.

The probe sequence for key $k$ is: $$h(k, 0),\ h(k, 1),\ h(k, 2),\ \ldots$$

where $h(k, i)$ is the $i$-th probe position. Different probe sequences give different open addressing variants — linear probing, quadratic probing, and double hashing.

---

## The Deletion Problem

Open addressing has a subtle issue with deletion. Simply marking a slot as empty breaks search — a probe sequence might have jumped over that slot to find a key further along, and clearing it would make that key unfindable.

The solution is **lazy deletion** — mark deleted slots with a special tombstone marker. Search skips tombstones but insert can reuse them.

```cpp
enum SlotState { EMPTY, OCCUPIED, DELETED };

struct Slot {
    int key, value;
    SlotState state = EMPTY;
};
```

---

## Load Factor Constraint

Unlike chaining, open addressing requires $\alpha < 1$ — you cannot store more elements than slots. In practice, keep $\alpha \leq 0.7$ to maintain good performance. As $\alpha \to 1$, probe sequences grow very long.

|Load Factor $\alpha$|Expected probes (successful search)|
|---|---|
|0.5|~1.5|
|0.7|~2.2|
|0.9|~5.5|
|0.99|~50|

> 📖 For open addressing analysis: [CLRS Chapter 11.4](https://mitpress.mit.edu/9780262046305/introduction-to-algorithms/)

---

Here is `06_03_04_Linear_Probing.md`:

---

# Linear Probing

The simplest open addressing strategy — when slot $h(k)$ is occupied, try the next slot, then the next, and so on.

$$h(k, i) = (h(k) + i) \mod m$$

```cpp
#include <iostream>
#include <vector>
using namespace std;

class LinearProbing {
    int m;
    vector<int> keys, vals;
    vector<int> state;  // 0=empty, 1=occupied, 2=deleted
    int n = 0;

    int hash(int key) { return key % m; }

public:
    LinearProbing(int size) : m(size), keys(size), vals(size), state(size, 0) {}

    // O(1) average, O(n) worst
    void insert(int key, int val) {
        int i = hash(key);
        while (state[i] == 1 && keys[i] != key)
            i = (i + 1) % m;
        if (state[i] != 1) n++;
        keys[i] = key; vals[i] = val; state[i] = 1;
    }

    // O(1) average, O(n) worst
    int search(int key) {
        int i = hash(key);
        while (state[i] != 0) {
            if (state[i] == 1 && keys[i] == key) return vals[i];
            i = (i + 1) % m;
        }
        return -1;
    }

    // Lazy deletion — O(1) average
    void remove(int key) {
        int i = hash(key);
        while (state[i] != 0) {
            if (state[i] == 1 && keys[i] == key) {
                state[i] = 2;  // tombstone
                n--;
                return;
            }
            i = (i + 1) % m;
        }
    }
};
```

---

## Primary Clustering

Linear probing suffers from **primary clustering** — occupied slots tend to form long contiguous runs. Once a cluster forms, any key hashing into it extends it further, making future insertions and searches progressively slower.

> **Definition.** _Primary clustering_ in linear probing is the tendency for long runs of occupied consecutive slots to form, because any key landing anywhere in a cluster extends that cluster.

This is why quadratic probing and double hashing were developed — they spread probes out to avoid clustering.

**Complexity:** Average $O(1)$ with low load factor, degrades badly as $\alpha \to 1$.

---

Here is `06_03_05_Quadratic_Probing.md`:

---

# Quadratic Probing

Instead of probing consecutive slots, quadratic probing jumps by increasing quadratic offsets:

$$h(k, i) = (h(k) + c_1 i + c_2 i^2) \mod m$$

Common choice: $c_1 = 0$, $c_2 = 1$, so $h(k,i) = (h(k) + i^2) \mod m$.

```cpp
class QuadraticProbing {
    int m;
    vector<int> keys, vals;
    vector<int> state;

    int hash(int key) { return key % m; }

public:
    QuadraticProbing(int size) : m(size), keys(size), vals(size), state(size, 0) {}

    void insert(int key, int val) {
        int base = hash(key);
        for (int i = 0; i < m; i++) {
            int idx = (base + i*i) % m;
            if (state[idx] != 1 || keys[idx] == key) {
                keys[idx] = key; vals[idx] = val; state[idx] = 1;
                return;
            }
        }
    }

    int search(int key) {
        int base = hash(key);
        for (int i = 0; i < m; i++) {
            int idx = (base + i*i) % m;
            if (state[idx] == 0) return -1;
            if (state[idx] == 1 && keys[idx] == key) return vals[idx];
        }
        return -1;
    }
};
```

---

## Secondary Clustering

Quadratic probing eliminates primary clustering but introduces **secondary clustering** — keys that hash to the same initial slot follow the exact same probe sequence, so they still compete with each other.

> **Definition.** _Secondary clustering_ occurs when keys with the same initial hash value follow identical probe sequences, competing for the same slots even though they are distinct keys.

**Important constraint** — quadratic probing is guaranteed to find an empty slot only if $m$ is prime and $\alpha \leq 0.5$. If these conditions are not met, the probe sequence may not cover all slots and insertion can fail even when slots are available.

---

Here is `06_03_06_Double_Hashing.md`:

---

# Double Hashing

Double hashing eliminates both primary and secondary clustering by using a _second hash function_ to determine the probe step size. Different keys get different step sizes, so their probe sequences are truly independent.

$$h(k, i) = (h_1(k) + i \cdot h_2(k)) \mod m$$

where $h_1$ is the primary hash and $h_2$ is the step size hash.

> **Definition.** _Double hashing_ uses two independent hash functions — $h_1$ determines the starting slot and $h_2$ determines the probe step size, ensuring different keys follow different probe sequences.

Requirements for $h_2$:

- $h_2(k) \neq 0$ for all $k$ — a zero step would probe the same slot forever
- $h_2(k)$ must be coprime with $m$ — ensures the full table is covered

A common choice: $h_2(k) = R - (k \mod R)$ where $R$ is a prime smaller than $m$.

```cpp
#include <iostream>
#include <vector>
using namespace std;

class DoubleHashing {
    int m, R;
    vector<int> keys, vals;
    vector<int> state;

    int h1(int key) { return key % m; }
    int h2(int key) { return R - (key % R); }  // R is prime < m

public:
    DoubleHashing(int size) : m(size), R(size - 1), // R should be prime
        keys(size), vals(size), state(size, 0) {
        // Ideally find largest prime < m for R
    }

    void insert(int key, int val) {
        int start = h1(key);
        int step  = h2(key);
        for (int i = 0; i < m; i++) {
            int idx = (start + i * step) % m;
            if (state[idx] != 1 || keys[idx] == key) {
                keys[idx] = key; vals[idx] = val; state[idx] = 1;
                return;
            }
        }
    }

    int search(int key) {
        int start = h1(key);
        int step  = h2(key);
        for (int i = 0; i < m; i++) {
            int idx = (start + i * step) % m;
            if (state[idx] == 0) return -1;
            if (state[idx] == 1 && keys[idx] == key) return vals[idx];
        }
        return -1;
    }
};

int main() {
    DoubleHashing ht(11);
    ht.insert(20, 200);
    ht.insert(31, 310);  // 31%11=9, 20%11=9 — collision
    cout << ht.search(20) << endl;  // 200
    cout << ht.search(31) << endl;  // 310
    return 0;
}
```

**Complexity:** Average $O(1)$, closest to theoretical ideal among open addressing methods.

---

Here is `06_03_07_Collision_Analysis.md`:

---

# Collision Analysis

Now that we have seen all three collision resolution strategies, let us compare them rigorously and understand when each is appropriate.

---

## Expected Number of Probes

For a hash table with load factor $\alpha = n/m$, the expected number of probes for a **unsuccessful search** (worst case for any operation):

|Method|Unsuccessful Search|Successful Search|
|---|---|---|
|Separate Chaining|$1 + \alpha$|$1 + \alpha/2$|
|Linear Probing|$\frac{1}{2}\left(1 + \frac{1}{(1-\alpha)^2}\right)$|$\frac{1}{2}\left(1 + \frac{1}{1-\alpha}\right)$|
|Quadratic Probing|$\frac{1}{1-\alpha}$|$-\frac{1}{\alpha}\ln(1-\alpha)$|
|Double Hashing|$\frac{1}{1-\alpha}$|$-\frac{1}{\alpha}\ln(1-\alpha)$|

At $\alpha = 0.5$:

|Method|Unsuccessful|Successful|
|---|---|---|
|Chaining|1.5|1.25|
|Linear Probing|2.5|1.5|
|Double Hashing|2.0|1.39|

---

## Head-to-Head Comparison

||Separate Chaining|Linear Probing|Quadratic|Double Hashing|
|---|---|---|---|---|
|Collision handling|External lists|Sequential scan|Quadratic jumps|Two hash functions|
|Clustering|None|Primary|Secondary|None|
|Load factor limit|Any $\alpha$|$\alpha < 1$|$\alpha \leq 0.5$|$\alpha < 1$|
|Cache performance|Poor (pointer chase)|Excellent|Good|Poor|
|Delete complexity|Simple|Tombstone needed|Tombstone needed|Tombstone needed|
|Implementation|Simple|Simple|Moderate|Moderate|

---

## When to Use What

**Separate chaining** — when load factor may exceed 0.7, when deletion is frequent (no tombstone needed), when cache misses are acceptable.

**Linear probing** — when cache performance is critical and load factor stays below 0.7. Modern CPUs make linear probing surprisingly fast in practice despite clustering.

**Double hashing** — when theoretical uniformity matters more than cache performance and load factor may approach 0.9.

In practice — just use `std::unordered_map`. It uses separate chaining internally and handles rehashing automatically.

$$\text{Expected operations} = O\left(\frac{1}{1-\alpha}\right) \text{ for open addressing}$$

$$\text{Expected operations} = O(1 + \alpha) \text{ for chaining}$$

Both are $O(1)$ when $\alpha$ is bounded by a constant.

> 📖 For full proofs: [CLRS Chapter 11](https://mitpress.mit.edu/9780262046305/introduction-to-algorithms/)

---

Now the Applications subfolder. Here is `06_04_01_Frequency_Counting.md`:

---

# Frequency Counting

One of the most natural uses of a hash table — count how many times each element appears in a collection. The key is the element, the value is its count.

> **Pattern.** _Frequency counting_ uses a hash map where keys are elements and values are their occurrence counts. Each element takes $O(1)$ to process, giving $O(n)$ total.

```cpp
#include <iostream>
#include <unordered_map>
#include <vector>
#include <string>
using namespace std;

// Count frequency of integers — O(n) time, O(k) space where k = distinct elements
unordered_map<int, int> countFreq(vector<int>& arr) {
    unordered_map<int, int> freq;
    for (int x : arr) freq[x]++;
    return freq;
}

// Find the most frequent element — O(n)
int mostFrequent(vector<int>& arr) {
    unordered_map<int, int> freq;
    for (int x : arr) freq[x]++;
    int maxFreq = 0, result = -1;
    for (auto& [k, v] : freq)
        if (v > maxFreq) { maxFreq = v; result = k; }
    return result;
}

// Find first non-repeating character — O(n)
char firstUnique(const string& s) {
    unordered_map<char, int> freq;
    for (char c : s) freq[c]++;
    for (char c : s)
        if (freq[c] == 1) return c;
    return '\0';
}

// Top k frequent elements — O(n log k)
// Uses hash map + min-heap of size k
#include <queue>
vector<int> topKFrequent(vector<int>& arr, int k) {
    unordered_map<int, int> freq;
    for (int x : arr) freq[x]++;

    priority_queue<pair<int,int>, vector<pair<int,int>>, greater<>> minPQ;
    for (auto& [val, cnt] : freq) {
        minPQ.push({cnt, val});
        if (minPQ.size() > k) minPQ.pop();
    }

    vector<int> result;
    while (!minPQ.empty()) {
        result.push_back(minPQ.top().second);
        minPQ.pop();
    }
    return result;
}

int main() {
    vector<int> arr = {1, 3, 2, 1, 4, 1, 3, 2, 1};
    auto freq = countFreq(arr);
    for (auto& [k, v] : freq)
        cout << k << ": " << v << endl;

    cout << "Most frequent: " << mostFrequent(arr) << endl;  // 1

    string s = "leetcode";
    cout << "First unique: " << firstUnique(s) << endl;  // l

    auto topK = topKFrequent(arr, 2);
    cout << "Top 2: ";
    for (int x : topK) cout << x << " ";  // 1 3 (or 1 2)
    return 0;
}
```

---

Here is `06_04_02_Anagram_Detection.md`:

---

# Anagram Detection

Two strings are **anagrams** if one is a rearrangement of the other — they contain exactly the same characters with the same frequencies. Hash maps make anagram detection clean and $O(n)$.

> **Definition.** Two strings are _anagrams_ if they have identical character frequency maps.

```cpp
#include <iostream>
#include <unordered_map>
#include <vector>
#include <string>
using namespace std;

// Check if two strings are anagrams — O(n)
bool isAnagram(const string& s, const string& t) {
    if (s.size() != t.size()) return false;
    unordered_map<char, int> freq;
    for (char c : s) freq[c]++;
    for (char c : t) {
        freq[c]--;
        if (freq[c] < 0) return false;
    }
    return true;
}

// Group anagrams together — O(n * k) where k = avg string length
vector<vector<string>> groupAnagrams(vector<string>& words) {
    unordered_map<string, vector<string>> groups;
    for (string& w : words) {
        string key = w;
        sort(key.begin(), key.end());  // sorted string as key
        groups[key].push_back(w);
    }
    vector<vector<string>> result;
    for (auto& [k, v] : groups) result.push_back(v);
    return result;
}

// Find all anagram substrings — sliding window + hash map — O(n)
vector<int> findAnagrams(const string& s, const string& p) {
    vector<int> result;
    if (s.size() < p.size()) return result;

    unordered_map<char, int> pFreq, windowFreq;
    for (char c : p) pFreq[c]++;

    int k = p.size();
    for (int i = 0; i < s.size(); i++) {
        windowFreq[s[i]]++;
        if (i >= k) {
            char out = s[i - k];
            windowFreq[out]--;
            if (windowFreq[out] == 0) windowFreq.erase(out);
        }
        if (i >= k - 1 && windowFreq == pFreq)
            result.push_back(i - k + 1);
    }
    return result;
}

int main() {
    cout << isAnagram("anagram", "nagaram") << endl;  // 1
    cout << isAnagram("rat", "car") << endl;           // 0

    vector<string> words = {"eat","tea","tan","ate","nat","bat"};
    auto groups = groupAnagrams(words);
    for (auto& g : groups) {
        for (auto& w : g) cout << w << " ";
        cout << endl;
    }

    auto indices = findAnagrams("cbaebabacd", "abc");
    for (int i : indices) cout << i << " ";  // 0 6
    return 0;
}
```

---

Here is `06_04_03_Subarray_Sum_Problems.md`:

---

# Subarray Sum Problems

Hash maps unlock a powerful technique for subarray problems — instead of nested loops to try every subarray, we use prefix sums combined with a hash map to find subarrays satisfying a condition in $O(n)$.

The core idea — if $\text{prefixSum}[j] - \text{prefixSum}[i] = \text{target}$, then the subarray from $i+1$ to $j$ sums to target. So we ask: for the current prefix sum, has a prefix sum of $\text{current} - \text{target}$ been seen before?

> **Pattern.** Store prefix sums in a hash map as you scan. At each position, check if $\text{prefixSum} - \text{target}$ exists in the map. If yes, a valid subarray ends here.

```cpp
#include <iostream>
#include <unordered_map>
#include <vector>
using namespace std;

// Count subarrays with sum = target — O(n), O(n)
int subarraySum(vector<int>& arr, int target) {
    unordered_map<int, int> prefixCount;
    prefixCount[0] = 1;  // empty prefix
    int sum = 0, count = 0;

    for (int x : arr) {
        sum += x;
        // If (sum - target) was seen before, those are valid subarrays
        if (prefixCount.count(sum - target))
            count += prefixCount[sum - target];
        prefixCount[sum]++;
    }
    return count;
}

// Longest subarray with sum = k — O(n), O(n)
int longestSubarraySum(vector<int>& arr, int k) {
    unordered_map<int, int> firstSeen;
    firstSeen[0] = -1;  // prefix sum 0 seen before index 0
    int sum = 0, maxLen = 0;

    for (int i = 0; i < arr.size(); i++) {
        sum += arr[i];
        if (firstSeen.count(sum - k))
            maxLen = max(maxLen, i - firstSeen[sum - k]);
        if (!firstSeen.count(sum))
            firstSeen[sum] = i;  // only store first occurrence
    }
    return maxLen;
}

// Check if subarray with sum = 0 exists — O(n)
bool zeroSumSubarray(vector<int>& arr) {
    unordered_map<int, bool> seen;
    seen[0] = true;
    int sum = 0;
    for (int x : arr) {
        sum += x;
        if (seen[sum]) return true;
        seen[sum] = true;
    }
    return false;
}

int main() {
    vector<int> arr = {1, 2, 3, -3, 1, 1, 1, 4, 2, -3};
    cout << subarraySum(arr, 3) << endl;        // count of subarrays summing to 3
    cout << longestSubarraySum(arr, 3) << endl; // length of longest such subarray

    vector<int> arr2 = {4, 2, -3, 1, 6};
    cout << zeroSumSubarray(arr2) << endl;  // 1 (true — subarray [2,-3,1])
    return 0;
}
```

---

Here is `06_QQ_Dictionaries_Quiz.md`:

---

## Quiz — Dictionaries

**Q1 — Identify the Complexity**

```cpp
unordered_map<int,int> mp;
for (int x : arr) mp[x]++;
for (auto& [k,v] : mp) cout << k << " " << v;
```

- [ ] $O(n)$
- [ ] $O(n \log n)$
- [ ] $\Theta(n)$
- [ ] $O(n^2)$

> [!answer]- Answer O(n) and Theta(n) are both correct. n insertions into unordered_map each O(1) average = O(n). Iterating over all entries = O(n). Total Theta(n) average. O(n^2) worst case is theoretically possible if all keys hash to same slot but practically never happens with a good hash function.

---

**Q2 — Hash Function** Which of the following is a bad hash function for string keys and why?

```cpp
int h1(string s, int m) { return s.size() % m; }
int h2(string s, int m) { int sum=0; for(char c:s) sum+=c; return sum%m; }
int h3(string s, int m) { long long h=0,p=31; for(char c:s){h=h*p+(c-'a'+1);} return h%m; }
```

> [!answer]- Answer h1 is worst — maps all strings of the same length to the same slot. All 3-letter words collide, all 4-letter words collide, etc. Catastrophic clustering. h2 is bad — "abc" and "bca" and "cab" all hash identically since addition is commutative. Order of characters is ignored. Many real-world strings are anagrams of each other. h3 is good — polynomial rolling hash. Each character's position affects the result due to multiplication by p^i. Small changes in input cause large changes in output.

---

**Q3 — Collision Resolution** A hash table of size 7 uses linear probing with $h(k) = k \mod 7$. Insert keys 50, 700, 76, 85, 92, 73, 101 in order. Show the final table state.

> [!answer]- Answer h(50)=1, h(700)=0, h(76)=6, h(85)=1→collision→2, h(92)=1→1 occupied→2 occupied→3, h(73)=3→collision→4, h(101)=3→3 occupied→4 occupied→5. Table: [700, 50, 85, 92, 73, 101, 76] Indices: [0, 1, 2, 3, 4, 5, 6] Notice the cluster forming around index 1-5 — primary clustering in action.

---

**Q4 — Subarray Sum** Array $[3, 4, 7, 2, -3, 1, 4, 2]$, target = 7. How many subarrays sum to 7? Trace the prefix sum hash map.

> [!answer]- Answer Prefix sums: 0,3,7,14,16,13,14,18,20. At each step check if prefixSum - 7 exists in map: sum=3: 3-7=-4 not seen. map={0:1,3:1}. sum=7: 7-7=0 seen once → count=1. map={0:1,3:1,7:1}. sum=14: 14-7=7 seen once → count=2. map adds 14. sum=16: 16-7=9 not seen. sum=13: 13-7=6 not seen. sum=14: 14-7=7 seen → count=3. map[14] becomes 2. sum=18: 18-7=11 not seen. sum=20: 20-7=13 seen → count=4. Answer: 4 subarrays.

---

**Q5 — True or False** Double hashing has no clustering problems and always achieves $O(1)$ average operations regardless of load factor.

- [ ] True
- [ ] False

> [!answer]- Answer False on the second part. Double hashing eliminates clustering (true) but performance still degrades as load factor approaches 1. At alpha=0.99, expected probes approaches 100. The O(1) average only holds when alpha is bounded by a constant below 1. At high load factors even double hashing becomes slow — this is why rehashing at alpha=0.75 is standard practice.

---

**Q6 — Write it Yourself** Given an array of integers, find the length of the longest consecutive sequence. Example: [100,4,200,1,3,2] → 4 (sequence 1,2,3,4). Must be O(n).

> [!answer]- Answer
> 
> ```cpp
> int longestConsecutive(vector<int>& arr) {
>     unordered_set<int> s(arr.begin(), arr.end());
>     int maxLen = 0;
>     for (int x : s) {
>         if (!s.count(x-1)) {  // x is start of a sequence
>             int len = 1;
>             while (s.count(x+len)) len++;
>             maxLen = max(maxLen, len);
>         }
>     }
>     return maxLen;
> }
> ```
> 
> Key insight: only start counting from the beginning of a sequence (x-1 not in set). This ensures each sequence is counted once. Each element is visited at most twice total across all sequences — O(n) overall.

---

**Q7 — Anagram Groups** Group these words into anagram families: [listen, silent, enlist, inlets, hello, world, dlrow].

> [!answer]- Answer Sort each word to get the key: listen→eilnst, silent→eilnst, enlist→eilnst, inlets→eilnst → all one group. hello→ehllo. world→dlorw, dlrow→dlorw → one group. Groups: [listen, silent, enlist, inlets], [hello], [world, dlrow].

---

And finally `06_00_Dictionaries.md`:

---

# Dictionaries

## Notes

- [[06_01_Dictionary_ADT]]
- [[06_02_Skip_Lists]]

## Subfolders

- [[06_03_00_Hash_Tables]]
- [[06_04_00_Applications]]

## Quiz

- [[06_QQ_Dictionaries_Quiz]]

---

## Quick Reference

### Dictionary ADT

> **Definition.** Maps keys to values. Supports insert, delete, search by key.

|Implementation|Search|Insert|Delete|Ordered|
|---|---|---|---|---|
|Hash Table|$O(1)$ avg|$O(1)$ avg|$O(1)$ avg|No|
|Skip List|$O(\log n)$ avg|$O(\log n)$ avg|$O(\log n)$ avg|Yes|
|Balanced BST|$O(\log n)$|$O(\log n)$|$O(\log n)$|Yes|

---

### Hash Function

$$h: U \rightarrow {0, 1, \ldots, m-1}$$

$$\text{Load factor: } \alpha = \frac{n}{m}$$

|Method|Formula|
|---|---|
|Division|$h(k) = k \mod m$|
|Multiplication|$h(k) = \lfloor m(kA \mod 1) \rfloor$, $A \approx 0.618$|
|String (polynomial)|$h(s) = \sum s[i] \cdot p^i \mod m$|

---

### Collision Resolution Comparison

|Method|Clustering|$\alpha$ limit|Cache|Delete|
|---|---|---|---|---|
|Chaining|None|Any|Poor|Simple|
|Linear Probing|Primary|$< 1$|Excellent|Tombstone|
|Quadratic|Secondary|$\leq 0.5$|Good|Tombstone|
|Double Hashing|None|$< 1$|Poor|Tombstone|

Expected probes at load factor $\alpha$: $$\text{Chaining: } O(1 + \alpha) \qquad \text{Open Addressing: } O\left(\frac{1}{1-\alpha}\right)$$

---

### Key Hash Map Patterns

**Frequency counting:**

```cpp
unordered_map<int,int> freq;
for (int x : arr) freq[x]++;
```

**Subarray sum = target:**

```cpp
unordered_map<int,int> seen; seen[0]=1;
int sum=0, count=0;
for (int x : arr) { sum+=x; count+=seen[sum-target]; seen[sum]++; }
```

**Anagram key:**

```cpp
string key = word; sort(key.begin(), key.end());
```

---

### Skip List

$$\text{Expected levels} = O(\log n), \quad \text{Promotion probability} = p = 0.5$$

$$\text{Expected search cost} = O(\log n) \text{ average}, \quad O(n) \text{ worst}$$

---
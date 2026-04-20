
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

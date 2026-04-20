
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

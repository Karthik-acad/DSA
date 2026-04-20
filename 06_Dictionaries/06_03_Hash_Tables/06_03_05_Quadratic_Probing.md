
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

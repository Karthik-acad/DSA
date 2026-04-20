
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

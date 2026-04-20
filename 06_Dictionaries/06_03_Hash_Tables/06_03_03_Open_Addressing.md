

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

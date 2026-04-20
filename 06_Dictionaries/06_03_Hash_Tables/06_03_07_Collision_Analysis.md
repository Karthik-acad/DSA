
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

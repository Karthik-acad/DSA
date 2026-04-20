
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
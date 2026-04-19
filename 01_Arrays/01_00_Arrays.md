
# Arrays

## Notes

- [[01_01_Static_and_Dynamic_Arrays]]
- [[01_02_Prefix_Sums]]
- [[01_03_Sliding_Window]]
- [[01_04_Two_Pointers]]
- [[01_05_Kadane_Max_Subarray]]

## Quiz

- [[01_QQ_Arrays_Quiz]]

---

## Quick Reference

### Array Fundamentals

> **Definition.** An _array_ is a collection of same-typed elements stored in contiguous memory, accessible via index in $O(1)$ using: $$\text{address}(i) = \text{base} + i \times \text{sizeof(element)}$$

|Property|Static Array|Dynamic Array (vector)|
|---|---|---|
|Size|Fixed at compile time|Grows at runtime|
|Access|$O(1)$|$O(1)$|
|Insert at end|Not applicable|$O(1)$ amortized|
|Insert at middle|Not applicable|$O(n)$|
|Delete at middle|Not applicable|$O(n)$|
|Memory overhead|None|size + capacity stored|

---

### Prefix Sums

Build once, query many times: $$P[i] = \sum_{j=0}^{i} A[j]$$ $$\text{sum}(l,r) = P[r] - P[l-1]$$

|Operation|Time|Space|
|---|---|---|
|Build|$O(n)$|$O(n)$|
|Query|$O(1)$|—|

---

### Sliding Window

| Variant         | When to use                              | Time   |
| --------------- | ---------------------------------------- | ------ |
| Fixed window    | Fixed size $k$ subarray problems         | $O(n)$ |
| Variable window | Smallest/largest subarray with condition | $O(n)$ |

Pattern for variable window — expand right pointer, shrink left pointer when condition is violated.

---

### Two Pointers

Requires sorted array for most use cases. Place left at start, right at end, move based on comparison to target:

- Sum too small → move left right
- Sum too large → move right left

|Problem|Time|Space|
|---|---|---|
|Two sum (sorted)|$O(n)$|$O(1)$|
|Three sum|$O(n^2)$|$O(1)$|
|Remove duplicates|$O(n)$|$O(1)$|

---

### Kadane's Algorithm
$$\text{current}[i] = \max(A[i],\ \text{current}[i-1] + A[i])$$ $$\text{answer} = \max_{i}\ \text{current}[i]$$

|Operation|Time|Space|
|---|---|---|
|Maximum subarray sum|$O(n)$|$O(1)$|

Rule of thumb — if running sum goes negative, reset. A negative prefix can only hurt future subarrays.

---
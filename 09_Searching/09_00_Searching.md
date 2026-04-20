
---

# Searching

## Notes

- [[09_01_Linear_Search]]
- [[09_02_Binary_Search]]
- [[09_03_Binary_Search_on_Answer]]
- [[09_04_Ternary_Search]]

## Quiz

- [[09_QQ_Searching_Quiz]]

---

## Quick Reference

### Comparison

|Algorithm|Requirement|Time|Space|
|---|---|---|---|
|Linear Search|None|$O(n)$|$O(1)$|
|Binary Search|Sorted array|$O(\log n)$|$O(1)$|
|Binary Search on Answer|Monotone feasibility|$O(f(n) \cdot \log(\text{range}))$|$O(1)$|
|Ternary Search|Unimodal function|$O(\log n)$|$O(1)$|

---

### Binary Search Templates

**Find exact value:**

```
lo=0, hi=n-1
while lo <= hi: mid=(lo+hi)/2
  arr[mid]==target → return mid
  arr[mid]<target  → lo=mid+1
  arr[mid]>target  → hi=mid-1
```

**Lower bound (first >= target):**

```
lo=0, hi=n
while lo < hi: mid=(lo+hi)/2
  arr[mid]<target → lo=mid+1
  else            → hi=mid
return lo
```

**Upper bound (first > target):**

```
lo=0, hi=n
while lo < hi: mid=(lo+hi)/2
  arr[mid]<=target → lo=mid+1
  else             → hi=mid
return lo
```

**Binary search on answer (find minimum feasible):**

```
lo=min_answer, hi=max_answer
while lo < hi: mid=(lo+hi)/2
  feasible(mid) → hi=mid
  else          → lo=mid+1
return lo
```

### Key Formula

$$\text{mid} = lo + \frac{hi - lo}{2} \quad \text{(never } \frac{lo+hi}{2} \text{ — overflow risk)}$$

$$\text{Binary search iterations} = \lceil \log_2(hi - lo + 1) \rceil$$

---


---

## Quiz — Searching

**Q1 — Binary Search Trace** Trace binary search for target=7 in $[1, 3, 5, 7, 9, 11, 13]$.

> [!answer]- Answer lo=0, hi=6. mid=3, arr[3]=7 == target. Found at index 3. One comparison — best case O(1). The target happened to be exactly at the midpoint.

**Q2 — Lower and Upper Bound** Array $[1, 2, 2, 2, 3, 4]$. Find lower_bound(2) and upper_bound(2). How many 2s are there?

> [!answer]- Answer lower_bound(2) = 1 (first index where arr[i] >= 2). upper_bound(2) = 4 (first index where arr[i] > 2). Count = upper_bound - lower_bound = 4 - 1 = 3. Three 2s at indices 1, 2, 3.

**Q3 — Overflow Bug** What is wrong with this binary search?

```cpp
while (left <= right) {
    int mid = (left + right) / 2;
    ...
}
```

> [!answer]- Answer Integer overflow. If left = 1,000,000,000 and right = 1,500,000,000 (both valid int values), left + right = 2,500,000,000 which overflows int (max ~2.1 billion). The result is undefined behavior. Fix: int mid = left + (right - left) / 2. This computes the same value but never overflows since right - left is at most INT_MAX.

**Q4 — Binary Search on Answer** You need to paint n boards of lengths $[10, 20, 30, 40]$ using k=2 painters. Each painter paints a contiguous section. Find the minimum time needed (minimize maximum work assigned to any painter).

> [!answer]- Answer Answer space: lo = max(arr) = 40 (each painter does at least one board), hi = sum(arr) = 100 (one painter does everything). Feasibility check: can we partition into k=2 contiguous sections with max sum <= mid? Binary search: mid=70: painter 1 takes [10,20,30]=60 <= 70, painter 2 takes [40]=40 <= 70. Feasible. hi=70. mid=55: painter 1 takes [10,20,30]=60 > 55, try [10,20]=30, painter 2 takes [30,40]=70 > 55. Not feasible. lo=56. ... converges to 60. Answer: 60 (painter 1: [10,20,30], painter 2: [40]).

**Q5 — True or False** Ternary search can find a value in a sorted array faster than binary search.

- [ ] True
- [ ] False

> [!answer]- Answer False. Ternary search is for finding the extremum of a unimodal function, not for finding a value in a sorted array. For sorted arrays, binary search (O(log_2 n)) is faster than ternary search (O(log_{3/2} n) ≈ 2.4 * log_2 n) — binary search eliminates half the space per step while ternary only eliminates one third.

**Q6 — Write it Yourself** Given a sorted array of distinct integers, find if there exists an index $i$ such that $arr[i] = i$. Do it in $O(\log n)$.

> [!answer]- Answer
> 
> ```cpp
> int fixedPoint(vector<int>& arr) {
>     int lo = 0, hi = arr.size() - 1;
>     while (lo <= hi) {
>         int mid = lo + (hi - lo) / 2;
>         if (arr[mid] == mid) return mid;
>         else if (arr[mid] < mid) lo = mid + 1;
>         else hi = mid - 1;
>     }
>     return -1;
> }
> ```
> 
> Key insight: since array is sorted with distinct integers, if arr[mid] > mid then arr[i] > i for all i >= mid (values grow faster than indices). Similarly if arr[mid] < mid then arr[i] < i for all i <= mid. This monotone property lets us binary search.

**Q7 — Identify the Complexity**

```cpp
int lo = 1, hi = n * n;
while (lo < hi) {
    int mid = lo + (hi - lo) / 2;
    if (feasible(mid)) hi = mid;
    else lo = mid + 1;
}
```

where `feasible` runs in $O(n)$.

> [!answer]- Answer O(n log(n^2)) = O(n * 2 log n) = O(n log n). The binary search runs O(log(hi - lo)) = O(log n^2) = O(2 log n) = O(log n) iterations, each calling feasible in O(n). Total O(n log n).

---

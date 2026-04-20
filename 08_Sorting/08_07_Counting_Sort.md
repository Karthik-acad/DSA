
---

# Counting Sort

If the range of values in the array is known and small, we can do even better — instead of sorting by comparisons or digit by digit, count how many times each value appears and reconstruct the sorted array directly.

> **Definition.** _Counting sort_ counts the frequency of each distinct element in the range $[0, k]$, computes cumulative counts to determine positions, then places each element in its correct sorted position.

```cpp
#include <iostream>
#include <vector>
using namespace std;

// Time: O(n + k) where k = range of values
// Space: O(n + k)
void countingSort(vector<int>& arr, int k) {
    vector<int> count(k + 1, 0);
    vector<int> output(arr.size());

    // Phase 1 — count frequencies
    for (int x : arr) count[x]++;

    // Phase 2 — cumulative count (position of each element)
    for (int i = 1; i <= k; i++) count[i] += count[i-1];

    // Phase 3 — place elements in output (right to left for stability)
    for (int i = arr.size() - 1; i >= 0; i--) {
        output[--count[arr[i]]] = arr[i];
    }

    arr = output;
}

int main() {
    vector<int> arr = {4, 2, 2, 8, 3, 3, 1};
    countingSort(arr, 8);  // max value is 8
    for (int x : arr) cout << x << " ";  // 1 2 2 3 3 4 8
    return 0;
}
```

---

## When Counting Sort Beats Everything

For $n$ elements with values in range $[0, k]$:

$$T(n) = O(n + k)$$

When $k = O(n)$, this is $O(n)$ — linear time. This is the subroutine radix sort uses at each digit position with $k = b-1$ (base minus 1).

When $k \gg n$ (say $k = 10^9$ for 32-bit integers), counting sort is impractical — the count array alone would use gigabytes of memory.

---

## Complexity Summary

|Case|Time|Space|
|---|---|---|
|All cases|$O(n + k)$|$O(n + k)$|
|Stable|Yes|—|

Best when: $k = O(n)$, elements are non-negative integers, range is known. Radix sort uses counting sort internally to achieve $O(n)$ for larger integer ranges.

---

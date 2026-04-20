
---

# Linear Search

The simplest possible search — scan every element from left to right until the target is found or the array is exhausted.

> **Definition.** _Linear search_ (sequential search) scans each element of a collection one by one until the target is found or all elements have been checked.

```cpp
#include <iostream>
#include <vector>
using namespace std;

// Time: O(n), Space: O(1)
int linearSearch(const vector<int>& arr, int target) {
    for (int i = 0; i < arr.size(); i++)
        if (arr[i] == target) return i;  // return index
    return -1;  // not found
}

// Works on unsorted arrays — binary search does not
// Works on any data type with equality comparison
int main() {
    vector<int> arr = {64, 25, 12, 22, 11};
    cout << linearSearch(arr, 22) << endl;  // 3
    cout << linearSearch(arr, 99) << endl;  // -1
    return 0;
}
```

---

## When Linear Search is the Right Choice

Linear search seems trivially worse than binary search — $O(n)$ vs $O(\log n)$. But it is the correct choice when:

- **Array is unsorted** — binary search requires sorted input
- **Single search on small array** — sorting costs $O(n \log n)$, which dominates if you only search once
- **Linked list** — binary search needs $O(1)$ random access, which linked lists do not provide
- **External data** (streaming) — data arrives one element at a time

---

## Complexity Summary

|Case|Time|Space|
|---|---|---|
|Best (found at index 0)|$O(1)$|$O(1)$|
|Average|$O(n)$|$O(1)$|
|Worst (not found)|$O(n)$|$O(1)$|

---

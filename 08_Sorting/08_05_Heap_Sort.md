
---

# Heap Sort

Heap sort was covered in detail in [[05_04_Heap_Sort]]. This note summarizes it in the context of the sorting chapter for comparison.

> **Definition.** _Heap sort_ uses a max-heap to sort in ascending order. Phase 1 builds a max-heap in $O(n)$. Phase 2 repeatedly extracts the maximum and places it at the end of the array, reducing heap size by 1 each time.

```cpp
#include <iostream>
#include <vector>
using namespace std;

void heapifyDown(vector<int>& arr, int n, int i) {
    int largest = i, left = 2*i+1, right = 2*i+2;
    if (left  < n && arr[left]  > arr[largest]) largest = left;
    if (right < n && arr[right] > arr[largest]) largest = right;
    if (largest != i) {
        swap(arr[i], arr[largest]);
        heapifyDown(arr, n, largest);
    }
}

// Time: O(n log n) all cases, Space: O(1) auxiliary
void heapSort(vector<int>& arr) {
    int n = arr.size();
    // Phase 1 — build max-heap O(n)
    for (int i = n/2 - 1; i >= 0; i--)
        heapifyDown(arr, n, i);
    // Phase 2 — extract max n times O(n log n)
    for (int i = n-1; i > 0; i--) {
        swap(arr[0], arr[i]);
        heapifyDown(arr, i, 0);
    }
}

int main() {
    vector<int> arr = {12, 11, 13, 5, 6, 7};
    heapSort(arr);
    for (int x : arr) cout << x << " ";  // 5 6 7 11 12 13
    return 0;
}
```

---

## Complexity Summary

|Case|Time|Space|
|---|---|---|
|Best|$\Theta(n \log n)$|$O(1)$|
|Average|$\Theta(n \log n)$|$O(1)$|
|Worst|$\Theta(n \log n)$|$O(1)$|
|Stable|No|—|

Heap sort is the only $O(n \log n)$ sort that is also $O(1)$ space — beating merge sort's $O(n)$ and quicksort's $O(\log n)$. But its cache performance is poor — heap accesses jump around the array unpredictably — making it slower than quicksort in practice despite equal asymptotic complexity.

---

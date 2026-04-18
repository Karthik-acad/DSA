
---

# Heap Sort

**Heap Sort** uses the heap data structure to sort an array. The idea is straightforward — build a max-heap from the array, then repeatedly extract the maximum and place it at the end.

> **Definition.** _Heap Sort_ is a comparison-based sorting algorithm that uses a max-heap to sort in ascending order. It has $O(n \log n)$ time complexity in all cases and $O(1)$ auxiliary space.

The two phases:

1. **Build max-heap** — $O(n)$
2. **Extract max $n$ times** — each extraction is $O(\log n)$, total $O(n \log n)$

$$T(n) = O(n) + O(n \log n) = O(n \log n)$$

```cpp
#include <iostream>
#include <vector>
using namespace std;

void heapifyDown(vector<int>& arr, int n, int i) {
    int largest = i;
    int left = 2*i + 1;
    int right = 2*i + 2;

    if (left  < n && arr[left]  > arr[largest]) largest = left;
    if (right < n && arr[right] > arr[largest]) largest = right;

    if (largest != i) {
        swap(arr[i], arr[largest]);
        heapifyDown(arr, n, largest);
    }
}

// Time: O(n log n), Space: O(1) auxiliary
void heapSort(vector<int>& arr) {
    int n = arr.size();

    // Phase 1 — Build max-heap in O(n)
    for (int i = n/2 - 1; i >= 0; i--)
        heapifyDown(arr, n, i);

    // Phase 2 — Extract max n times
    for (int i = n - 1; i > 0; i--) {
        // Current max (root) goes to its sorted position
        swap(arr[0], arr[i]);
        // Heapify the reduced heap
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

The trick that makes it $O(1)$ space — instead of extracting into a separate array, we swap the max to the end of the current array and reduce the heap size by 1. The sorted portion grows from right to left in-place.

---

## Heap Sort vs Other $O(n \log n)$ Sorts

| |Heap Sort|Merge Sort|Quick Sort|
|---|---|---|---|
|Time (worst)|$O(n \log n)$|$O(n \log n)$|$O(n^2)$|
|Time (average)|$O(n \log n)$|$O(n \log n)$|$O(n \log n)$|
|Space|$O(1)$|$O(n)$|$O(\log n)$|
|Stable|No|Yes|No (typically)|
|Cache|Poor|Good|Good|

Heap Sort has the best theoretical guarantee — $O(n \log n)$ worst case with $O(1)$ space. But in practice it is slower than Quick Sort due to poor cache behavior — heap access patterns jump around the array unpredictably, causing many cache misses.

> 📖 For sorting algorithm comparisons: [[08_00_Sorting]]

---

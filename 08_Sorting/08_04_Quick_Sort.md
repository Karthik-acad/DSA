
---

# Quick Sort

Merge sort guarantees $O(n \log n)$ but needs $O(n)$ extra space. **Quick sort** achieves $O(n \log n)$ average with $O(\log n)$ space and excellent cache performance — which is why `std::sort` is typically quick sort based.

> **Definition.** _Quick sort_ selects a **pivot** element, partitions the array into elements less than the pivot and elements greater than the pivot, then recursively sorts each partition. The pivot ends up in its final sorted position after partitioning.

The key operation is **partition** — rearrange the array so all elements less than the pivot are to its left and all greater are to its right.

```cpp
#include <iostream>
#include <vector>
using namespace std;

// Lomuto partition scheme
// Places pivot at correct position, returns pivot index
// Time: O(n), Space: O(1)
int partition(vector<int>& arr, int low, int high) {
    int pivot = arr[high];  // last element as pivot
    int i = low - 1;        // boundary of smaller elements

    for (int j = low; j < high; j++) {
        if (arr[j] <= pivot) {
            i++;
            swap(arr[i], arr[j]);
        }
    }
    swap(arr[i + 1], arr[high]);  // place pivot in correct position
    return i + 1;
}

// Time: O(n log n) average, O(n^2) worst
// Space: O(log n) average call stack
void quickSort(vector<int>& arr, int low, int high) {
    if (low >= high) return;
    int pivotIdx = partition(arr, low, high);
    quickSort(arr, low, pivotIdx - 1);
    quickSort(arr, pivotIdx + 1, high);
}

int main() {
    vector<int> arr = {10, 7, 8, 9, 1, 5};
    quickSort(arr, 0, arr.size() - 1);
    for (int x : arr) cout << x << " ";  // 1 5 7 8 9 10
    return 0;
}
```

---

## Pivot Selection and Worst Case

The worst case for quick sort is $O(n^2)$ — when the pivot is always the smallest or largest element, creating partitions of size 0 and $n-1$. This happens exactly when the input is sorted or reverse-sorted and you always pick the first or last element as pivot.

**Fixes:**

**Random pivot** — pick a random element as pivot. Makes worst case astronomically unlikely.

```cpp
int partitionRandom(vector<int>& arr, int low, int high) {
    int randIdx = low + rand() % (high - low + 1);
    swap(arr[randIdx], arr[high]);  // move random pivot to end
    return partition(arr, low, high);
}
```

**Median of three** — take median of first, middle, last elements as pivot. Avoids sorted-input worst case.

```cpp
int medianOfThree(vector<int>& arr, int low, int high) {
    int mid = low + (high - low) / 2;
    if (arr[low] > arr[mid])   swap(arr[low], arr[mid]);
    if (arr[low] > arr[high])  swap(arr[low], arr[high]);
    if (arr[mid] > arr[high])  swap(arr[mid], arr[high]);
    // arr[mid] is now median — move to end-1 as pivot
    swap(arr[mid], arr[high]);
    return partition(arr, low, high);
}
```

---

## Hoare Partition (Alternative)

The original partition scheme by Hoare — uses two pointers moving toward each other. Fewer swaps than Lomuto on average.

```cpp
int hoarePartition(vector<int>& arr, int low, int high) {
    int pivot = arr[low + (high - low) / 2];
    int i = low - 1, j = high + 1;
    while (true) {
        do { i++; } while (arr[i] < pivot);
        do { j--; } while (arr[j] > pivot);
        if (i >= j) return j;
        swap(arr[i], arr[j]);
    }
}
```

---

## Recurrence

Best/average case — balanced partitions: $$T(n) = 2T(n/2) + O(n) = \Theta(n \log n)$$

Worst case — one empty partition: $$T(n) = T(n-1) + O(n) = \Theta(n^2)$$

---

## Complexity Summary

|Case|Time|Space (call stack)|
|---|---|---|
|Best|$\Theta(n \log n)$|$O(\log n)$|
|Average|$\Theta(n \log n)$|$O(\log n)$|
|Worst|$\Theta(n^2)$|$O(n)$|
|Stable|No|—|

Quick sort is generally faster than merge sort in practice despite equal asymptotic complexity — partition is cache-friendly (sequential access) while merge requires copying to a temporary array.

> 📖 For `std::sort` implementation details: [GCC Introsort](https://en.wikipedia.org/wiki/Introsort) — combines quicksort, heapsort, and insertion sort.

---


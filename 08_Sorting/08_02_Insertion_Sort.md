
Here is `08_02_Insertion_Sort.md`:

---

# Insertion Sort

Where selection sort always scans the entire unsorted portion, **insertion sort** takes each new element and inserts it into its correct position in the already-sorted left portion — like how you sort cards in your hand one by one.

> **Definition.** _Insertion sort_ maintains a sorted left portion. For each new element, it shifts larger sorted elements right to make room, then places the new element in its correct position.

```cpp
#include <iostream>
#include <vector>
using namespace std;

// Time: O(n^2) worst, O(n) best (already sorted)
// Space: O(1) auxiliary
void insertionSort(vector<int>& arr) {
    int n = arr.size();
    for (int i = 1; i < n; i++) {
        int key = arr[i];   // element to insert
        int j = i - 1;
        // Shift elements greater than key one position right
        while (j >= 0 && arr[j] > key) {
            arr[j + 1] = arr[j];
            j--;
        }
        arr[j + 1] = key;  // insert key in correct position
    }
}

int main() {
    vector<int> arr = {12, 11, 13, 5, 6};
    insertionSort(arr);
    for (int x : arr) cout << x << " ";  // 5 6 11 12 13
    return 0;
}
```

---

## Why Insertion Sort is Special

Despite being $O(n^2)$ in general, insertion sort has properties that make it genuinely useful:

**Best case $O(n)$** — on a sorted array, the while loop never executes. Each element is already in place. This makes it ideal for nearly-sorted data.

**Online algorithm** — it can sort elements as they arrive one by one without seeing the full input first.

**In-place and stable** — no extra space, equal elements maintain relative order.

**Low overhead** — for small arrays ($n \leq 20$), insertion sort beats merge sort and quick sort due to zero overhead. This is why `std::sort` typically switches to insertion sort for small subarrays.

---

## Number of Inversions

> **Definition.** An _inversion_ is a pair $(i, j)$ where $i < j$ but $arr[i] > arr[j]$ — a pair that is out of order.

Insertion sort makes exactly one comparison and one shift per inversion. So:

$$\text{total operations} = \text{number of inversions} + n$$

- Sorted array: 0 inversions → $O(n)$
- Reverse sorted: $n(n-1)/2$ inversions → $O(n^2)$
- Random: expected $n(n-1)/4$ inversions → $O(n^2)$ average

---

## Complexity Summary

|Case|Time|Space|
|---|---|---|
|Best (sorted)|$O(n)$|$O(1)$|
|Average|$O(n^2)$|$O(1)$|
|Worst (reverse sorted)|$O(n^2)$|$O(1)$|
|Stable|Yes|—|

---


Starting with Sorting.

Here is `08_01_Selection_Sort.md`:

---

# Selection Sort

From our understanding of arrays, we know that finding the minimum element requires scanning the entire array — $O(n)$. **Selection sort** builds on this simple idea: repeatedly find the minimum of the unsorted portion and place it at the front.

Think of sorting a hand of playing cards by repeatedly picking the smallest card from the remaining unsorted cards and placing it in its correct position from left to right.

> **Definition.** _Selection sort_ divides the array into a sorted left portion and unsorted right portion. In each pass it selects the minimum element from the unsorted portion and swaps it to the boundary between sorted and unsorted.

```cpp
#include <iostream>
#include <vector>
using namespace std;

// Time: O(n^2) always — even if sorted
// Space: O(1) auxiliary
void selectionSort(vector<int>& arr) {
    int n = arr.size();
    for (int i = 0; i < n - 1; i++) {
        // Find minimum in unsorted portion [i, n-1]
        int minIdx = i;
        for (int j = i + 1; j < n; j++)
            if (arr[j] < arr[minIdx])
                minIdx = j;
        // Swap minimum to sorted boundary
        swap(arr[i], arr[minIdx]);
    }
}

int main() {
    vector<int> arr = {64, 25, 12, 22, 11};
    selectionSort(arr);
    for (int x : arr) cout << x << " ";  // 11 12 22 25 64
    return 0;
}
```

---

## Analysis

The outer loop runs $n-1$ times. The inner loop runs $n-1, n-2, \ldots, 1$ times respectively. Total comparisons:

$$(n-1) + (n-2) + \ldots + 1 = \frac{n(n-1)}{2} = \Theta(n^2)$$

This is always $\Theta(n^2)$ — selection sort makes no use of existing order in the array. Even a sorted array takes the same number of comparisons. This makes it strictly worse than insertion sort for nearly-sorted data.

The one advantage — selection sort makes exactly $n-1$ swaps (one per pass). If swapping is expensive (say, swapping large objects on disk), selection sort's swap efficiency can matter.

---

## Stability

Selection sort is **not stable** by default — the swap can move equal elements past each other. Example: $[2_a, 2_b, 1]$ — when 1 swaps with $2_a$, the relative order of the two 2s is disrupted.

It can be made stable by using insertion instead of swapping (shift elements right rather than swap), but this removes its swap-count advantage.

---

## Complexity Summary

|Case|Time|Space|
|---|---|---|
|Best|$\Theta(n^2)$|$O(1)$|
|Average|$\Theta(n^2)$|$O(1)$|
|Worst|$\Theta(n^2)$|$O(1)$|
|Swaps|$\Theta(n)$|—|

---

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

Here is `08_03_Merge_Sort.md`:

---

# Merge Sort

Selection and insertion sort are both $O(n^2)$ — fine for small arrays but impractical for large ones. **Merge sort** achieves $O(n \log n)$ using divide and conquer — split the array in half, sort each half recursively, then merge the two sorted halves.

> **Definition.** _Merge sort_ is a divide-and-conquer sorting algorithm. It divides the array into two halves, recursively sorts each half, and merges the two sorted halves into a single sorted array.

The key insight — merging two sorted arrays of sizes $n/2$ takes $O(n)$. Doing this at every level of $\log n$ levels gives $O(n \log n)$ total.

```cpp
#include <iostream>
#include <vector>
using namespace std;

// Merge two sorted subarrays arr[left..mid] and arr[mid+1..right]
// Time: O(n), Space: O(n)
void merge(vector<int>& arr, int left, int mid, int right) {
    vector<int> temp(right - left + 1);
    int i = left, j = mid + 1, k = 0;

    while (i <= mid && j <= right) {
        if (arr[i] <= arr[j]) temp[k++] = arr[i++];
        else                  temp[k++] = arr[j++];
    }
    while (i <= mid)    temp[k++] = arr[i++];
    while (j <= right)  temp[k++] = arr[j++];

    for (int x = 0; x < temp.size(); x++)
        arr[left + x] = temp[x];
}

// Time: O(n log n), Space: O(n)
void mergeSort(vector<int>& arr, int left, int right) {
    if (left >= right) return;  // base case — single element
    int mid = left + (right - left) / 2;
    mergeSort(arr, left, mid);
    mergeSort(arr, mid + 1, right);
    merge(arr, left, mid, right);
}

int main() {
    vector<int> arr = {38, 27, 43, 3, 9, 82, 10};
    mergeSort(arr, 0, arr.size() - 1);
    for (int x : arr) cout << x << " ";  // 3 9 10 27 38 43 82
    return 0;
}
```

---

## Recurrence

$$T(n) = 2T(n/2) + O(n)$$

By Master Theorem — $a=2$, $b=2$, $n^{\log_2 2} = n$. $f(n) = O(n) = \Theta(n)$ — Case 2:

$$T(n) = \Theta(n \log n)$$

---

## Counting Inversions

Merge sort can count inversions in $O(n \log n)$ — during the merge step, whenever an element from the right half is placed before elements from the left half, those are inversions. The count equals how many elements remain in the left half at that moment.

```cpp
long long mergeCount(vector<int>& arr, int left, int right) {
    if (left >= right) return 0;
    int mid = left + (right - left) / 2;
    long long count = 0;
    count += mergeCount(arr, left, mid);
    count += mergeCount(arr, mid+1, right);

    // Count during merge
    vector<int> temp;
    int i = left, j = mid + 1;
    while (i <= mid && j <= right) {
        if (arr[i] <= arr[j]) { temp.push_back(arr[i++]); }
        else {
            count += (mid - i + 1);  // all remaining left elements form inversions
            temp.push_back(arr[j++]);
        }
    }
    while (i <= mid)   temp.push_back(arr[i++]);
    while (j <= right) temp.push_back(arr[j++]);
    for (int x = 0; x < temp.size(); x++) arr[left+x] = temp[x];
    return count;
}
```

---

## Complexity Summary

|Case|Time|Space|
|---|---|---|
|Best|$\Theta(n \log n)$|$O(n)$|
|Average|$\Theta(n \log n)$|$O(n)$|
|Worst|$\Theta(n \log n)$|$O(n)$|
|Stable|Yes|—|

The $O(n)$ space is merge sort's main weakness compared to quicksort's $O(\log n)$. In-place merge sort exists but is significantly more complex and has worse cache performance.

---

Here is `08_04_Quick_Sort.md`:

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

Here is `08_05_Heap_Sort.md`:

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

Here is `08_06_Radix_Sort.md`:

---

# Radix Sort

All the sorting algorithms so far are **comparison-based** — they sort by comparing pairs of elements. There is a theoretical lower bound for comparison-based sorting:

> **Theorem.** Any comparison-based sorting algorithm requires $\Omega(n \log n)$ comparisons in the worst case.

**Radix sort** breaks this barrier by not comparing elements at all — instead it sorts digit by digit. The tradeoff is that it only works on integers (or strings) with a bounded number of digits.

> **Definition.** _Radix sort_ sorts integers by processing digits from least significant to most significant (LSD radix sort), using a stable sort (typically counting sort) at each digit position.

The key requirement — the sort at each digit position must be **stable**. This ensures that previously established order among digits is preserved.

```cpp
#include <iostream>
#include <vector>
using namespace std;

// Counting sort on digit at position exp (1, 10, 100, ...)
// Time: O(n + b) where b = base (10)
void countingByDigit(vector<int>& arr, int exp) {
    int n = arr.size();
    vector<int> output(n), count(10, 0);

    // Count occurrences of each digit
    for (int i = 0; i < n; i++)
        count[(arr[i] / exp) % 10]++;

    // Cumulative count — position in output
    for (int i = 1; i < 10; i++)
        count[i] += count[i-1];

    // Build output array right to left (for stability)
    for (int i = n-1; i >= 0; i--) {
        int digit = (arr[i] / exp) % 10;
        output[--count[digit]] = arr[i];
    }

    arr = output;
}

// Time: O(d * (n + b)) where d = digits, b = base
// Space: O(n + b)
void radixSort(vector<int>& arr) {
    int maxVal = *max_element(arr.begin(), arr.end());
    // Process each digit position
    for (int exp = 1; maxVal / exp > 0; exp *= 10)
        countingByDigit(arr, exp);
}

int main() {
    vector<int> arr = {170, 45, 75, 90, 802, 24, 2, 66};
    radixSort(arr);
    for (int x : arr) cout << x << " ";  // 2 24 45 66 75 90 170 802
    return 0;
}
```

---

## Complexity Analysis

For $n$ numbers each with at most $d$ digits in base $b$:

$$T(n) = O(d \cdot (n + b))$$

If $d$ is constant and $b = O(n)$:

$$T(n) = O(n)$$

This beats the $\Omega(n \log n)$ comparison barrier — but only because we use extra information about the structure of the keys (they are integers with bounded digits).

---

## LSD vs MSD Radix Sort

**LSD (Least Significant Digit)** — process from rightmost digit left. Simpler, works well for fixed-length keys.

**MSD (Most Significant Digit)** — process from leftmost digit right. More complex but can stop early and works for variable-length strings.

---

## Complexity Summary

|Case|Time|Space|
|---|---|---|
|All cases|$O(d(n+b))$|$O(n+b)$|
|Stable|Yes|—|

Best when: keys are integers, $d$ is small, $n$ is large. Not suitable for floating-point numbers or complex comparison keys.

---

Here is `08_07_Counting_Sort.md`:

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

Here is `08_08_Stability_and_Comparisons.md`:

---

# Stability and Comparisons

Now that we have seen all the sorting algorithms, it is worth stepping back and understanding the key properties that distinguish them — and when to reach for which one.

---

## Stability

> **Definition.** A sorting algorithm is _stable_ if equal elements maintain their original relative order after sorting.

Example: sorting $[(2, \text{Alice}), (1, \text{Bob}), (2, \text{Charlie})]$ by number. A stable sort gives $[(1, \text{Bob}), (2, \text{Alice}), (2, \text{Charlie})]$ — Alice before Charlie because she appeared first. An unstable sort might give Alice and Charlie in either order.

Stability matters when:

- Sorting by multiple keys (sort by last name, then stably sort by first name)
- The original order carries meaning

|Algorithm|Stable|
|---|---|
|Selection Sort|No|
|Insertion Sort|Yes|
|Merge Sort|Yes|
|Quick Sort|No (typically)|
|Heap Sort|No|
|Counting Sort|Yes|
|Radix Sort|Yes (uses stable subroutine)|

---

## The Comparison Lower Bound

> **Theorem.** Any comparison-based sorting algorithm requires $\Omega(n \log n)$ comparisons in the worst case.

**Proof sketch:** There are $n!$ possible orderings of $n$ elements. A comparison-based sort builds a decision tree where each internal node is a comparison and each leaf is a permutation. The tree must have at least $n!$ leaves, so its height is at least $\log_2(n!) \approx n \log n$ by Stirling's approximation.

$$\log_2(n!) = \sum_{i=1}^{n} \log_2 i \geq \frac{n}{2} \log_2 \frac{n}{2} = \Omega(n \log n)$$

This means merge sort and heap sort are **asymptotically optimal** among comparison-based sorts.

---

## Comprehensive Comparison

|Algorithm|Best|Average|Worst|Space|Stable|Notes|
|---|---|---|---|---|---|---|
|Selection|$\Theta(n^2)$|$\Theta(n^2)$|$\Theta(n^2)$|$O(1)$|No|Min swaps|
|Insertion|$O(n)$|$O(n^2)$|$O(n^2)$|$O(1)$|Yes|Best for small/nearly-sorted|
|Merge|$\Theta(n\log n)$|$\Theta(n\log n)$|$\Theta(n\log n)$|$O(n)$|Yes|Guaranteed $n\log n$|
|Quick|$\Theta(n\log n)$|$\Theta(n\log n)$|$\Theta(n^2)$|$O(\log n)$|No|Fastest in practice|
|Heap|$\Theta(n\log n)$|$\Theta(n\log n)$|$\Theta(n\log n)$|$O(1)$|No|Best worst-case + space|
|Counting|$O(n+k)$|$O(n+k)$|$O(n+k)$|$O(n+k)$|Yes|Only integers, small range|
|Radix|$O(d(n+b))$|$O(d(n+b))$|$O(d(n+b))$|$O(n+b)$|Yes|Beats $n\log n$ for integers|

---

## Decision Guide

**Need guaranteed $O(n \log n)$ with $O(1)$ space?** → Heap sort

**Need stable $O(n \log n)$?** → Merge sort

**Need fastest in practice, space not critical?** → Quick sort with random pivot

**Nearly sorted data?** → Insertion sort

**Small array ($n \leq 20$)?** → Insertion sort

**Integer keys, known small range?** → Counting sort

**Large integers, multiple digits?** → Radix sort

**Default choice in C++?** → `std::sort` (introsort: quicksort + heapsort + insertion sort hybrid)

---

Now the quiz. Here is `08_QQ_Sorting_Quiz.md`:

---

## Quiz — Sorting

**Q1 — Identify the Sort** Which sorting algorithm performs $\Theta(n^2)$ comparisons even on a sorted array?

- [ ] Insertion Sort
- [ ] Selection Sort
- [ ] Merge Sort
- [ ] Quick Sort

> [!answer]- Answer Selection Sort. It always scans the full unsorted portion to find the minimum regardless of existing order — O(n^2) always. Insertion sort is O(n) on sorted input. Merge sort is always O(n log n). Quick sort with good pivot is O(n log n) on sorted input but O(n^2) with bad pivot choice.

**Q2 — Trace Selection Sort** Trace selection sort on $[64, 25, 12, 22, 11]$. Show the array after each pass.

> [!answer]- Answer Pass 1: min=11 at idx 4, swap with idx 0: [11, 25, 12, 22, 64]. Pass 2: min=12 at idx 2, swap with idx 1: [11, 12, 25, 22, 64]. Pass 3: min=22 at idx 3, swap with idx 2: [11, 12, 22, 25, 64]. Pass 4: min=25 at idx 3, swap with idx 3 (no change): [11, 12, 22, 25, 64]. Final: [11, 12, 22, 25, 64]. Total swaps = 4 = n-1.

**Q3 — Inversions** How many inversions does $[5, 3, 1, 4, 2]$ have? How many operations will insertion sort take?

> [!answer]- Answer Inversions: (5,3), (5,1), (5,4), (5,2), (3,1), (3,2), (4,2) = 7 inversions. Insertion sort operations = inversions + n = 7 + 5 = 12 total comparisons/shifts.

**Q4 — Merge Sort Recurrence** What is the recurrence for merge sort and what does the Master Theorem give?

> [!answer]- Answer T(n) = 2T(n/2) + O(n). Master Theorem: a=2, b=2, n^(log_2 2) = n. f(n) = O(n) = Theta(n) — Case 2. Result: T(n) = Theta(n log n). The O(n) merge step at each of log n levels gives n log n total.

**Q5 — Quick Sort Worst Case** On what input does quick sort with last-element pivot perform worst? What is the recurrence and result?

> [!answer]- Answer Already sorted (ascending or descending) input. Last element is always the minimum or maximum, creating partitions of size 0 and n-1. Recurrence: T(n) = T(0) + T(n-1) + O(n) = T(n-1) + O(n). Expanding: T(n) = O(n) + O(n-1) + ... + O(1) = O(n^2). Fix: use random pivot or median-of-three to avoid this degenerate case.

**Q6 — Stability** You have records $(2, \text{Bob}), (1, \text{Alice}), (2, \text{Charlie}), (1, \text{Dave})$. Sort by the number. Which algorithms guarantee Bob appears before Charlie and Alice before Dave?

> [!answer]- Answer Stable sorts: insertion sort, merge sort, counting sort, radix sort. These guarantee Bob before Charlie (both 2, original order preserved) and Alice before Dave (both 1, original order preserved). Unstable sorts (selection, quick, heap) may reorder equal elements — no guarantee on relative order of ties.

**Q7 — Counting Sort** Sort $[3, 0, 2, 1, 3, 2, 0]$ using counting sort. Trace the count array and output.

> [!answer]- Answer Range k=3. Count: [2, 1, 2, 2] (0 appears 2x, 1 appears 1x, 2 appears 2x, 3 appears 2x). Cumulative: [2, 3, 5, 7]. Build output right to left: arr[6]=0 → output[--count[0]]=output[1]=0. arr[5]=2 → output[4]=2. arr[4]=3 → output[6]=3. arr[3]=1 → output[2]=1. arr[2]=2 → output[3]=2. arr[1]=0 → output[0]=0. arr[0]=3 → output[5]=3. Output: [0, 0, 1, 2, 2, 3, 3].

**Q8 — Write it Yourself** Implement merge sort and use it to count inversions in $[6, 5, 4, 3, 2, 1]$. How many inversions are there and verify with the formula $n(n-1)/2$.

> [!answer]- Answer n=6, inversions = 6*5/2 = 15. Every pair is inverted since array is reverse sorted. Merge sort inversion count: each merge step counts inversions where right half elements are placed before left half elements. For a fully reverse-sorted array of size 6 this gives 15 total across all merges. Verification: pairs (6,5),(6,4),(6,3),(6,2),(6,1),(5,4),(5,3),(5,2),(5,1),(4,3),(4,2),(4,1),(3,2),(3,1),(2,1) = 15 pairs. Confirmed.

---

And `08_00_Sorting.md`:

---

# Sorting

## Notes

- [[08_01_Selection_Sort]]
- [[08_02_Insertion_Sort]]
- [[08_03_Merge_Sort]]
- [[08_04_Quick_Sort]]
- [[08_05_Heap_Sort]]
- [[08_06_Radix_Sort]]
- [[08_07_Counting_Sort]]
- [[08_08_Stability_and_Comparisons]]

## Quiz

- [[08_QQ_Sorting_Quiz]]

---

## Quick Reference

### Comparison Lower Bound

$$\text{Any comparison sort: } \Omega(n \log n) \text{ worst case}$$ $$\log_2(n!) \approx n \log n \text{ (Stirling)}$$

### Full Comparison Table

|Algorithm|Best|Average|Worst|Space|Stable|
|---|---|---|---|---|---|
|Selection|$\Theta(n^2)$|$\Theta(n^2)$|$\Theta(n^2)$|$O(1)$|No|
|Insertion|$O(n)$|$O(n^2)$|$O(n^2)$|$O(1)$|Yes|
|Merge|$\Theta(n\log n)$|$\Theta(n\log n)$|$\Theta(n\log n)$|$O(n)$|Yes|
|Quick|$\Theta(n\log n)$|$\Theta(n\log n)$|$\Theta(n^2)$|$O(\log n)$|No|
|Heap|$\Theta(n\log n)$|$\Theta(n\log n)$|$\Theta(n\log n)$|$O(1)$|No|
|Counting|$O(n+k)$|$O(n+k)$|$O(n+k)$|$O(n+k)$|Yes|
|Radix|$O(d(n+b))$|$O(d(n+b))$|$O(d(n+b))$|$O(n+b)$|Yes|

### Recurrences

$$\text{Merge Sort: } T(n) = 2T(n/2) + O(n) = \Theta(n \log n)$$ $$\text{Quick Sort (avg): } T(n) = 2T(n/2) + O(n) = \Theta(n \log n)$$ $$\text{Quick Sort (worst): } T(n) = T(n-1) + O(n) = \Theta(n^2)$$

### Inversions

$$\text{Insertion sort operations} = \text{inversions} + n$$ $$\text{Max inversions (reverse sorted)} = \frac{n(n-1)}{2}$$

---

Now Searching. Here is `09_01_Linear_Search.md`:

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

Here is `09_02_Binary_Search.md`:

---

# Binary Search

If the array is sorted, we can do much better than linear scan. **Binary search** exploits sorted order to eliminate half the search space at each step — achieving $O(\log n)$.

> **Definition.** _Binary search_ operates on a sorted array. At each step it compares the target with the middle element and eliminates the half that cannot contain the target, recursing on the remaining half.

Think of finding a word in a dictionary — you open to the middle, see whether your word comes before or after, and eliminate the irrelevant half. You repeat this, each time halving the remaining pages.

```cpp
#include <iostream>
#include <vector>
using namespace std;

// Iterative — Time: O(log n), Space: O(1)
int binarySearch(const vector<int>& arr, int target) {
    int left = 0, right = arr.size() - 1;
    while (left <= right) {
        int mid = left + (right - left) / 2;  // avoids overflow vs (left+right)/2
        if (arr[mid] == target) return mid;
        else if (arr[mid] < target) left = mid + 1;
        else right = mid - 1;
    }
    return -1;
}

// Recursive — Time: O(log n), Space: O(log n)
int binarySearchRec(const vector<int>& arr, int target, int left, int right) {
    if (left > right) return -1;
    int mid = left + (right - left) / 2;
    if (arr[mid] == target) return mid;
    if (arr[mid] < target) return binarySearchRec(arr, target, mid+1, right);
    return binarySearchRec(arr, target, left, mid-1);
}

int main() {
    vector<int> arr = {2, 3, 4, 10, 40};
    cout << binarySearch(arr, 10) << endl;  // 3
    cout << binarySearch(arr, 5)  << endl;  // -1
    return 0;
}
```

---

## Lower Bound and Upper Bound

Two essential variants — finding the first or last occurrence of a target (or the insertion point for a target not in the array).

```cpp
// Lower bound — first index where arr[i] >= target
// C++ equivalent: lower_bound(arr.begin(), arr.end(), target)
int lowerBound(const vector<int>& arr, int target) {
    int left = 0, right = arr.size();  // right = n, not n-1
    while (left < right) {
        int mid = left + (right - left) / 2;
        if (arr[mid] < target) left = mid + 1;
        else right = mid;
    }
    return left;  // first position >= target
}

// Upper bound — first index where arr[i] > target
// C++ equivalent: upper_bound(arr.begin(), arr.end(), target)
int upperBound(const vector<int>& arr, int target) {
    int left = 0, right = arr.size();
    while (left < right) {
        int mid = left + (right - left) / 2;
        if (arr[mid] <= target) left = mid + 1;
        else right = mid;
    }
    return left;  // first position > target
}

// Count occurrences of target using lower and upper bound
int countOccurrences(const vector<int>& arr, int target) {
    return upperBound(arr, target) - lowerBound(arr, target);
}
```

---

## The Overflow Bug

A classic bug:

```cpp
int mid = (left + right) / 2;  // WRONG — can overflow if left + right > INT_MAX
int mid = left + (right - left) / 2;  // CORRECT — no overflow
```

Always use the second form.

---

## Complexity Summary

|Case|Time|Space (iterative)|
|---|---|---|
|Best (found at mid)|$O(1)$|$O(1)$|
|Average|$O(\log n)$|$O(1)$|
|Worst|$O(\log n)$|$O(1)$|

> 📖 For binary search bugs and edge cases: [Nearly All Binary Searches are Broken — Joshua Bloch](https://ai.googleblog.com/2006/06/extra-extra-read-all-about-it-nearly.html)

---

Here is `09_03_Binary_Search_on_Answer.md`:

---

# Binary Search on Answer

Binary search is not just for searching sorted arrays — it is a powerful technique for optimization problems where you need to find the minimum or maximum value satisfying some condition. Instead of searching for a value _in_ an array, you binary search over the _answer space_.

> **Definition.** _Binary search on answer_ applies binary search to the range of possible answers $[\text{lo}, \text{hi}]$. At each step it checks whether a candidate answer $\text{mid}$ is feasible. The search narrows the range based on the feasibility check.

The key insight — the feasibility function must be **monotone**: if answer $x$ is feasible, then all answers $> x$ (or $< x$) are also feasible. This creates the sorted-like property that binary search needs.

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

// Example 1 — Minimum capacity to ship packages in D days
// Packages have weights. We need minimum ship capacity such that
// all packages can be shipped in at most D days.
// Feasibility: can we ship everything within D days at capacity C?
bool canShip(vector<int>& weights, int D, int capacity) {
    int days = 1, load = 0;
    for (int w : weights) {
        if (load + w > capacity) { days++; load = 0; }
        load += w;
    }
    return days <= D;
}

// Time: O(n log(sum - max)) where sum = total weight, max = max single weight
int shipWithinDays(vector<int>& weights, int D) {
    int lo = *max_element(weights.begin(), weights.end());  // min possible capacity
    int hi = 0; for (int w : weights) hi += w;              // max possible capacity

    while (lo < hi) {
        int mid = lo + (hi - lo) / 2;
        if (canShip(weights, D, mid)) hi = mid;  // mid works, try smaller
        else lo = mid + 1;                        // mid too small, try larger
    }
    return lo;
}

// Example 2 — Find square root (integer)
int mySqrt(int x) {
    if (x < 2) return x;
    long long lo = 1, hi = x / 2;
    while (lo < hi) {
        long long mid = lo + (hi - lo + 1) / 2;  // upper mid
        if (mid * mid <= x) lo = mid;
        else hi = mid - 1;
    }
    return lo;
}

// Example 3 — Koko eating bananas
// n piles of bananas, h hours. Find minimum eating speed k such that
// Koko can finish all bananas in h hours.
bool canFinish(vector<int>& piles, int h, int k) {
    int hours = 0;
    for (int p : piles)
        hours += (p + k - 1) / k;  // ceil(p/k)
    return hours <= h;
}

int minEatingSpeed(vector<int>& piles, int h) {
    int lo = 1, hi = *max_element(piles.begin(), piles.end());
    while (lo < hi) {
        int mid = lo + (hi - lo) / 2;
        if (canFinish(piles, h, mid)) hi = mid;
        else lo = mid + 1;
    }
    return lo;
}

int main() {
    vector<int> weights = {1,2,3,4,5,6,7,8,9,10};
    cout << shipWithinDays(weights, 5) << endl;  // 15

    cout << mySqrt(8) << endl;  // 2

    vector<int> piles = {3,6,7,11};
    cout << minEatingSpeed(piles, 8) << endl;  // 4
    return 0;
}
```

---

## The Template

Binary search on answer always follows this pattern:

```
lo = minimum possible answer
hi = maximum possible answer

while lo < hi:
    mid = lo + (hi - lo) / 2
    if feasible(mid):
        hi = mid        # mid works, search left (find minimum)
    else:
        lo = mid + 1    # mid too small, search right

return lo
```

For finding maximum feasible answer, flip the condition.

---

## Complexity

Each binary search on answer does $O(\log(\text{hi} - \text{lo}))$ iterations, each calling the feasibility check. If the check costs $O(f(n))$:

$$T = O(f(n) \cdot \log(\text{hi} - \text{lo}))$$

---

Here is `09_04_Ternary_Search.md`:

---

# Ternary Search

Binary search finds a value in a sorted array. **Ternary search** finds the maximum or minimum of a **unimodal function** — one that strictly increases then strictly decreases (or vice versa) with a single peak.

> **Definition.** _Ternary search_ finds the maximum of a unimodal function $f$ on interval $[lo, hi]$ by dividing the interval into three parts and eliminating the third that cannot contain the maximum.

At each step, compute $m_1 = lo + (hi-lo)/3$ and $m_2 = hi - (hi-lo)/3$:

- If $f(m_1) < f(m_2)$ → maximum is in $[m_1, hi]$, eliminate left third
- If $f(m_1) > f(m_2)$ → maximum is in $[lo, m_2]$, eliminate right third

```cpp
#include <iostream>
#include <functional>
using namespace std;

// Find maximum of unimodal function on [lo, hi]
// Time: O(log(hi - lo)), Space: O(1)
double ternarySearch(double lo, double hi, function<double(double)> f) {
    while (hi - lo > 1e-9) {
        double m1 = lo + (hi - lo) / 3;
        double m2 = hi - (hi - lo) / 3;
        if (f(m1) < f(m2)) lo = m1;
        else hi = m2;
    }
    return (lo + hi) / 2;
}

// For integer domains — O(log n)
int ternarySearchInt(int lo, int hi, function<int(int)> f) {
    while (hi - lo > 2) {
        int m1 = lo + (hi - lo) / 3;
        int m2 = hi - (hi - lo) / 3;
        if (f(m1) < f(m2)) lo = m1;
        else hi = m2;
    }
    // Check remaining candidates
    int best = lo;
    for (int i = lo+1; i <= hi; i++)
        if (f(i) > f(best)) best = i;
    return best;
}

int main() {
    // Find maximum of -(x-3)^2 + 9 — peak at x=3
    auto f = [](double x) { return -(x-3)*(x-3) + 9; };
    double peak = ternarySearch(0, 10, f);
    cout << "Peak at x = " << peak << endl;  // ~3.0
    cout << "f(peak) = " << f(peak) << endl;  // ~9.0
    return 0;
}
```

---

## Ternary vs Binary Search

||Binary Search|Ternary Search|
|---|---|---|
|Input requirement|Sorted array|Unimodal function|
|Eliminates per step|Half|One-third|
|Iterations|$O(\log_2 n)$|$O(\log_{3/2} n) \approx 2.4\log_2 n$|
|Use case|Finding value|Finding max/min of unimodal function|

Interestingly, ternary search is slightly _slower_ than binary search asymptotically — eliminating a third versus a half means more iterations. For unimodal functions on a discrete domain, you can often use binary search on the derivative instead (checking if $f(m) < f(m+1)$) to achieve the same $O(\log n)$ with fewer constant factors.

---

## Complexity Summary

|Case|Time|Space|
|---|---|---|
|All cases|$O(\log n)$ or $O(\log(\epsilon^{-1}))$ for continuous|$O(1)$|

---

Here is `09_QQ_Searching_Quiz.md`:

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

And `09_00_Searching.md`:

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
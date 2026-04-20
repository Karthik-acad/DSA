
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


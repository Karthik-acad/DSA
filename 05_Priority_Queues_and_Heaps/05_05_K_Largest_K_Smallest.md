
---

# K Largest and K Smallest Elements

Given an array of $n$ elements, find the $k$ largest (or smallest) elements. This comes up constantly in real systems — top-k search results, top-k scores, top-k trending items.

There are several approaches with different tradeoffs:

---

## Approach 1 — Sort

Sort the array and take the last $k$ elements.

- Time: $O(n \log n)$
- Space: $O(1)$
- Simple but overkill — we sort the entire array when we only need $k$ elements.

---

## Approach 2 — Min-Heap of Size k (Best for K Largest)

Maintain a min-heap of size $k$. For each new element — if it is larger than the heap's minimum, pop the minimum and push the new element. At the end, the heap contains the $k$ largest elements.

> **Why a min-heap for k largest?** The min-heap lets you quickly check and evict the smallest element among your current top-k candidates. If the new element beats the current minimum, it deserves a spot.

```cpp
#include <iostream>
#include <vector>
#include <queue>
using namespace std;

// Time: O(n log k), Space: O(k)
vector<int> kLargest(vector<int>& arr, int k) {
    // Min-heap of size k
    priority_queue<int, vector<int>, greater<int>> minPQ;

    for (int x : arr) {
        minPQ.push(x);
        if (minPQ.size() > k)
            minPQ.pop();  // remove smallest — it does not belong in top k
    }

    vector<int> result;
    while (!minPQ.empty()) {
        result.push_back(minPQ.top());
        minPQ.pop();
    }
    return result;
}

// Time: O(n log k), Space: O(k)
vector<int> kSmallest(vector<int>& arr, int k) {
    // Max-heap of size k
    priority_queue<int> maxPQ;

    for (int x : arr) {
        maxPQ.push(x);
        if (maxPQ.size() > k)
            maxPQ.pop();  // remove largest — it does not belong in bottom k
    }

    vector<int> result;
    while (!maxPQ.empty()) {
        result.push_back(maxPQ.top());
        maxPQ.pop();
    }
    return result;
}

int main() {
    vector<int> arr = {7, 10, 4, 3, 20, 15};
    auto large = kLargest(arr, 3);
    auto small = kSmallest(arr, 3);

    cout << "3 largest: ";
    for (int x : large) cout << x << " ";  // 10 15 20 (any order)

    cout << "\n3 smallest: ";
    for (int x : small) cout << x << " ";  // 7 4 3 (any order)
    return 0;
}
```

---

## Approach 3 — QuickSelect ($O(n)$ average)

Based on the partition step of Quick Sort. Partition the array around a pivot — if the pivot lands at index $n-k$, everything to its right is a top-k element. Average $O(n)$ but $O(n^2)$ worst case.

```cpp
// Time: O(n) average, O(n^2) worst, Space: O(1)
int partition(vector<int>& arr, int left, int right) {
    int pivot = arr[right];
    int i = left - 1;
    for (int j = left; j < right; j++)
        if (arr[j] <= pivot) swap(arr[++i], arr[j]);
    swap(arr[i+1], arr[right]);
    return i + 1;
}

int quickSelect(vector<int>& arr, int left, int right, int k) {
    if (left == right) return arr[left];
    int pivotIdx = partition(arr, left, right);
    if (pivotIdx == k) return arr[pivotIdx];
    else if (k < pivotIdx) return quickSelect(arr, left, pivotIdx-1, k);
    else return quickSelect(arr, pivotIdx+1, right, k);
}

// Returns the k-th smallest element
int kthSmallest(vector<int>& arr, int k) {
    return quickSelect(arr, 0, arr.size()-1, k-1);
}
```

---

## Approach Comparison

|Approach|Time|Space|Best when|
|---|---|---|---|
|Sort|$O(n \log n)$|$O(1)$|$k \approx n$|
|Heap of size $k$|$O(n \log k)$|$O(k)$|$k \ll n$, streaming data|
|QuickSelect|$O(n)$ average|$O(1)$|Single query, $k$-th element|

The heap approach shines when $k$ is much smaller than $n$ — $O(n \log k)$ is far better than $O(n \log n)$ when $k$ is small. It also works on **streaming data** where you do not have all elements upfront.

---

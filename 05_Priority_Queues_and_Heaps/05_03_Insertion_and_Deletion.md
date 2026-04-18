
---

# Insertion and Deletion

The two core heap operations — insert and extractMax/extractMin — both run in $O(\log n)$. They work by temporarily violating the heap property and then restoring it through a process called **heapify**.

There are two directions of heapify:

> **Definition.** _Heapify-up_ (also called sift-up or bubble-up) moves a node _upward_ toward the root until the heap property is restored. Used after insertion.

> **Definition.** _Heapify-down_ (also called sift-down or bubble-down) moves a node _downward_ toward the leaves until the heap property is restored. Used after extraction.

---

## Insertion — Heapify Up

Add the new element at the end of the array (next available position in the complete binary tree), then bubble it up by swapping with its parent while it is larger than its parent.

$$T(n) = O(\log n) \text{ — tree height is } \lfloor \log_2 n \rfloor$$

```cpp
// Time: O(log n), Space: O(1)
void insert(int val) {
    heap.push_back(val);       // add at end
    int i = heap.size() - 1;   // index of new element

    // Heapify up — swap with parent while larger
    while (i > 0 && heap[parent(i)] < heap[i]) {
        swap(heap[parent(i)], heap[i]);
        i = parent(i);
    }
}
```

---

## Extraction — Heapify Down

The maximum is always at `heap[0]`. To extract it — swap root with the last element, remove the last element, then sink the new root down by swapping with its larger child until heap property is restored.

```cpp
// Time: O(log n), Space: O(1)
int extractMax() {
    if (isEmpty()) return -1;
    int maxVal = heap[0];

    // Move last element to root and remove last
    heap[0] = heap.back();
    heap.pop_back();

    // Heapify down
    heapifyDown(0);
    return maxVal;
}

void heapifyDown(int i) {
    int largest = i;
    int left = leftChild(i);
    int right = rightChild(i);
    int n = heap.size();

    if (left < n && heap[left] > heap[largest])
        largest = left;
    if (right < n && heap[right] > heap[largest])
        largest = right;

    if (largest != i) {
        swap(heap[i], heap[largest]);
        heapifyDown(largest);
    }
}
```

---

## Build Heap — Heapify an Entire Array

Given an unordered array, convert it to a heap. The naive approach inserts elements one by one — $O(n \log n)$. The smarter approach starts from the last internal node and heapifies down — $O(n)$.

> **Definition.** _Build-heap_ constructs a heap from an arbitrary array in $O(n)$ by calling heapify-down on every internal node from bottom to top.

$$\text{Last internal node index} = \lfloor n/2 \rfloor - 1$$

```cpp
// Time: O(n) — not O(n log n), see proof below
// Space: O(1)
void buildHeap(vector<int>& arr) {
    heap = arr;
    int n = heap.size();
    // Start from last internal node, go up to root
    for (int i = n/2 - 1; i >= 0; i--)
        heapifyDown(i);
}
```

**Why $O(n)$ and not $O(n \log n)$?** Most nodes are near the leaves and travel very little. Formally:

$$\sum_{h=0}^{\lfloor \log n \rfloor} \left\lceil \frac{n}{2^{h+1}} \right\rceil \cdot h = O(n)$$

Nodes at height $h$ take $O(h)$ to heapify down, and there are $\approx n/2^{h+1}$ such nodes. The sum converges to $O(n)$.

---

## Full MaxHeap Implementation

```cpp
#include <iostream>
#include <vector>
using namespace std;

class MaxHeap {
    vector<int> heap;

    int parent(int i)     { return (i - 1) / 2; }
    int leftChild(int i)  { return 2 * i + 1; }
    int rightChild(int i) { return 2 * i + 2; }

    void heapifyDown(int i) {
        int largest = i;
        int left = leftChild(i), right = rightChild(i);
        int n = heap.size();
        if (left  < n && heap[left]  > heap[largest]) largest = left;
        if (right < n && heap[right] > heap[largest]) largest = right;
        if (largest != i) {
            swap(heap[i], heap[largest]);
            heapifyDown(largest);
        }
    }

    void heapifyUp(int i) {
        while (i > 0 && heap[parent(i)] < heap[i]) {
            swap(heap[parent(i)], heap[i]);
            i = parent(i);
        }
    }

public:
    MaxHeap() {}
    MaxHeap(vector<int>& arr) {
        heap = arr;
        for (int i = arr.size()/2 - 1; i >= 0; i--)
            heapifyDown(i);
    }

    void insert(int val)  { heap.push_back(val); heapifyUp(heap.size()-1); }
    int  peek()           { return heap[0]; }
    bool isEmpty()        { return heap.empty(); }
    int  size()           { return heap.size(); }

    int extractMax() {
        int maxVal = heap[0];
        heap[0] = heap.back();
        heap.pop_back();
        if (!heap.empty()) heapifyDown(0);
        return maxVal;
    }

    void print() { for (int x : heap) cout << x << " "; cout << endl; }
};

int main() {
    vector<int> arr = {3, 1, 4, 1, 5, 9, 2, 6};
    MaxHeap h(arr);
    h.print();                        // 9 6 4 3 5 1 2 1 (heap order)
    cout << h.extractMax() << endl;   // 9
    cout << h.peek() << endl;         // 6
    h.insert(10);
    cout << h.peek() << endl;         // 10
    return 0;
}
```

---

## Complexity Summary

|Operation|Time|Space|
|---|---|---|
|Insert|$O(\log n)$|$O(1)$|
|Extract max/min|$O(\log n)$|$O(1)$|
|Peek|$O(1)$|$O(1)$|
|Build heap|$O(n)$|$O(1)$|
|Heapify up/down|$O(\log n)$|$O(\log n)$ recursive stack|

---

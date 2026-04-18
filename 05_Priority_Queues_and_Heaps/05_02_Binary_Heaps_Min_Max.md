
---

# Binary Heaps — Min and Max

The most common and efficient implementation of a priority queue is the **binary heap**. It is elegant because it looks like a tree conceptually but is stored as a plain array — no pointers needed.

> **Definition.** A _binary heap_ is a complete binary tree satisfying the **heap property**:
> 
> - **Max-heap:** every node's value is greater than or equal to its children's values. The maximum is always at the root.
> - **Min-heap:** every node's value is less than or equal to its children's values. The minimum is always at the root.

> **Definition.** A _complete binary tree_ is a binary tree where all levels are fully filled except possibly the last, which is filled from left to right.

The completeness property is what lets us store the tree in an array without any pointers — the parent-child relationships are encoded purely through index arithmetic.

---

## Array Representation

For a 1-indexed array:

$$\text{parent}(i) = \lfloor i/2 \rfloor$$ $$\text{leftChild}(i) = 2i$$ $$\text{rightChild}(i) = 2i + 1$$

For a 0-indexed array (more common in code):

$$\text{parent}(i) = \lfloor (i-1)/2 \rfloor$$ $$\text{leftChild}(i) = 2i + 1$$ $$\text{rightChild}(i) = 2i + 2$$

So the heap $[10, 7, 9, 3, 5, 6, 8]$ stored as an array represents:

```
        10
       /   \
      7     9
     / \   / \
    3   5 6   8
```

No pointers. No dynamic allocation. Just index math.

```cpp
#include <iostream>
#include <vector>
using namespace std;

class MaxHeap {
    vector<int> heap;

    int parent(int i) { return (i - 1) / 2; }
    int leftChild(int i) { return 2 * i + 1; }
    int rightChild(int i) { return 2 * i + 2; }

public:
    int size() { return heap.size(); }
    bool isEmpty() { return heap.empty(); }
    int peek() { return heap[0]; }  // O(1) — max is always at root

    void print() {
        for (int x : heap) cout << x << " ";
        cout << endl;
    }
};
```

---

## Heap Property Verification

```cpp
// Check if array represents a valid max-heap
// Time: O(n)
bool isMaxHeap(vector<int>& arr) {
    int n = arr.size();
    for (int i = 0; i <= (n - 2) / 2; i++) {
        if (2*i+1 < n && arr[i] < arr[2*i+1]) return false;
        if (2*i+2 < n && arr[i] < arr[2*i+2]) return false;
    }
    return true;
}
```

---

## Min-Heap vs Max-Heap

| |Max-Heap|Min-Heap|
|---|---|---|
|Root|Maximum element|Minimum element|
|peek()|$O(1)$ max|$O(1)$ min|
|Use case|Heap sort, K largest|Dijkstra, K smallest|
|C++ STL|Default `priority_queue`|`priority_queue` with `greater<int>`|

```cpp
#include <queue>
using namespace std;

// Max-heap (default)
priority_queue<int> maxPQ;

// Min-heap
priority_queue<int, vector<int>, greater<int>> minPQ;
```

> 📖 For heap theory and proofs: [CLRS — Introduction to Algorithms, Chapter 6](https://mitpress.mit.edu/9780262046305/introduction-to-algorithms/)

---

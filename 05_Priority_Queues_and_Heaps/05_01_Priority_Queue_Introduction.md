
---

# Priority Queue Introduction

From our study of queues, we know that a regular queue serves elements in the order they arrived — FIFO. But what if arrival order does not matter and instead each element has a _priority_, and the element with the highest priority should always be served first regardless of when it arrived?

Think of a hospital emergency room. Patients do not get treated in the order they walked in — a patient with a heart attack gets seen before someone with a sprained ankle, even if they arrived later. The priority (severity) determines the order, not arrival time.

This is exactly what a **priority queue** does.

> **Definition.** A _priority queue_ is an Abstract Data Type where each element has an associated priority, and the element with the highest (or lowest) priority is always accessed and removed first, regardless of insertion order.

|Operation|Description|Complexity|
|---|---|---|
|`insert(x, p)`|Insert element $x$ with priority $p$|$O(\log n)$|
|`extractMax()` / `extractMin()`|Remove and return highest/lowest priority element|$O(\log n)$|
|`peek()`|Return highest/lowest priority element without removing|$O(1)$|
|`isEmpty()`|Check if empty|$O(1)$|

Notice insert and extract are $O(\log n)$ — not $O(1)$ like a regular queue. This is the cost of maintaining the priority ordering. The underlying data structure that makes this efficient is the **heap**.

---

## Naive Implementations and Why They Fail

Before looking at heaps, consider why simpler implementations are insufficient:

|Implementation|Insert|ExtractMax|Problem|
|---|---|---|---|
|Unsorted array|$O(1)$|$O(n)$ — scan all|Extract too slow|
|Sorted array|$O(n)$ — shift|$O(1)$ — last element|Insert too slow|
|Sorted linked list|$O(n)$ — find position|$O(1)$|Insert too slow|
|**Binary Heap**|$O(\log n)$|$O(\log n)$|Balanced tradeoff|

The heap gives us $O(\log n)$ for both — a balanced tradeoff that makes priority queues practical for large inputs.

---

## Where Priority Queues Appear

**Dijkstra's shortest path** — always process the unvisited node with the smallest known distance. A min priority queue maintains this efficiently. You will see this in [[10_03_01_Dijkstras_Algorithm]].

**Huffman encoding** — always merge the two lowest frequency nodes. A min priority queue selects them in $O(\log n)$. You will see this in [[14_04_Huffman_Encoding]].

**CPU Scheduling** — processes with higher priority get CPU time first.

**Heap Sort** — repeatedly extract the maximum to sort in $O(n \log n)$.

**K largest / K smallest problems** — maintain a heap of size $k$ to find the top $k$ elements efficiently.

> 📖 For priority queue applications in competitive programming: [CP Algorithms — Priority Queue](https://cp-algorithms.com/data_structures/priority_queue.html)

---

Here is `05_05_K_Largest_K_Smallest.md`:

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

Here is `05_06_Median_in_Data_Stream.md`:

---

# Median in a Data Stream

Given a stream of integers arriving one by one, find the median after each insertion. You cannot sort after every insertion — that would be $O(n^2 \log n)$ total.

This is one of the most elegant heap problems. The key insight — split the data into two halves:

- The **lower half** maintained as a **max-heap** — its root is the largest of the smaller half
- The **upper half** maintained as a **min-heap** — its root is the smallest of the larger half

The median is always at the boundary between the two halves.

> **Definition.** The _two-heap median_ maintains a max-heap for the lower half and a min-heap for the upper half, keeping their sizes equal (or differing by at most 1). The median is the root of the larger heap, or the average of both roots if sizes are equal.

```cpp
#include <iostream>
#include <queue>
using namespace std;

class MedianFinder {
    // Max-heap — lower half
    priority_queue<int> lower;
    // Min-heap — upper half
    priority_queue<int, vector<int>, greater<int>> upper;

public:
    // Time: O(log n)
    void insert(int num) {
        // Step 1 — push to lower half
        lower.push(num);

        // Step 2 — balance: ensure lower.top() <= upper.top()
        if (!upper.empty() && lower.top() > upper.top()) {
            upper.push(lower.top());
            lower.pop();
        }

        // Step 3 — balance sizes: sizes differ by at most 1
        if (lower.size() > upper.size() + 1) {
            upper.push(lower.top());
            lower.pop();
        } else if (upper.size() > lower.size() + 1) {
            lower.push(upper.top());
            upper.pop();
        }
    }

    // Time: O(1)
    double getMedian() {
        if (lower.size() == upper.size())
            return (lower.top() + upper.top()) / 2.0;
        return lower.size() > upper.size() ? lower.top() : upper.top();
    }
};

int main() {
    MedianFinder mf;
    vector<int> stream = {5, 15, 1, 3, 2, 8, 7, 9, 10, 6, 11, 4};

    for (int x : stream) {
        mf.insert(x);
        cout << "Insert " << x << " → median: " << mf.getMedian() << endl;
    }
    return 0;
}
```

---

## Why This Works

At any point, the lower max-heap contains the smallest $\lceil n/2 \rceil$ elements and the upper min-heap contains the largest $\lfloor n/2 \rfloor$ elements. The heap roots are the two middle elements. The median is:

- If sizes equal → average of both roots
- If one is larger → root of the larger heap

Every insertion does at most 2 heap operations — $O(\log n)$ total per insertion.

---

## Complexity Summary

|Operation|Time|Space|
|---|---|---|
|Insert|$O(\log n)$|$O(1)$|
|Get median|$O(1)$|—|
|Total for $n$ insertions|$O(n \log n)$|$O(n)$|

> 📖 For more heap problems: [LeetCode Heap Problems](https://leetcode.com/tag/heap-priority-queue/)

---

Here is `05_QQ_Priority_Queues_Quiz.md`:

---

## Quiz — Priority Queues and Heaps

**Q1 — Identify the Complexity**

```cpp
priority_queue<int> pq;
for (int x : arr) pq.push(x);
while (!pq.empty()) {
    cout << pq.top();
    pq.pop();
}
```

- [ ] $O(n)$
- [ ] $O(n \log n)$
- [ ] $\Theta(n \log n)$
- [ ] $O(n^2)$

> [!answer]- Answer O(n log n) and Theta(n log n) are both correct. n pushes each costing O(log n) = O(n log n). n pops each costing O(log n) = O(n log n). Total = Theta(n log n). This is actually heap sort — printing elements in decreasing order. O(n) is false — each push/pop involves heapify operations costing O(log n).

---

**Q2 — Array to Heap** Given array $[3, 1, 4, 1, 5, 9, 2, 6]$, draw the max-heap tree and show the array representation after build-heap.

> [!answer]- Answer Build-heap starts from last internal node (index 3, value 1) and heapifies down. After build-heap array: [9, 6, 4, 3, 5, 1, 2, 1] (one valid answer — multiple valid max-heaps exist). Tree: 9 /  
> 6 4 / \ /  
> 3 5 1 2 / 1 Verify: every parent >= both children. Root 9 is maximum. Time O(n).

---

**Q3 — True or False** Building a heap by inserting elements one by one is $O(n \log n)$, but build-heap (heapify from bottom) is $O(n)$.

- [ ] True
- [ ] False

> [!answer]- Answer True. Inserting one by one: each of n insertions costs O(log n) in the worst case = O(n log n) total. Build-heap: most nodes are near the leaves and travel very little distance during heapify-down. The sum n/2 * 0 + n/4 * 1 + n/8 * 2 + ... converges to O(n). The two approaches give the same data structure but with different construction costs.

---

**Q4 — Trace Heap Sort** Trace heap sort on $[4, 10, 3, 5, 1]$. Show the array after build-heap and after each extraction.

> [!answer]- Answer After build-heap: [10, 5, 3, 4, 1]. Extract 1: swap root with last → [1,5,3,4,10], heapify [1,5,3,4] → [5,4,3,1,10]. Extract 2: swap → [1,4,3,5,10] heapify [1,4,3] → [4,1,3,5,10]. Extract 3: swap → [3,1,4,5,10] heapify [3,1] → [3,1,4,5,10]. Extract 4: swap → [1,3,4,5,10] heapify [1] → [1,3,4,5,10]. Final sorted: [1,3,4,5,10].

---

**Q5 — K Largest** Array $[2, 5, 1, 7, 3, 9, 4, 8, 6]$. Find the 4 largest elements using a min-heap of size 4. Trace the heap state as each element is processed.

> [!answer]- Answer Process 2: heap=[2]. Process 5: heap=[2,5]. Process 1: heap=[1,5,2] wait — min-heap so [1,2,5]. Process 7: size=4 heap=[1,2,5,7]. Process 3: 3>min(1) pop 1 push 3: heap=[2,3,5,7]. Process 9: 9>min(2) pop 2 push 9: heap=[3,5,9,7]. Process 4: 4>min(3) pop 3 push 4: heap=[4,5,9,7]. Process 8: 8>min(4) pop 4 push 8: heap=[5,7,9,8]. Process 6: 6>min(5) pop 5 push 6: heap=[6,7,9,8]. Final 4 largest: {6, 7, 8, 9}.

---

**Q6 — Median Stream** Insert the stream $[6, 3, 9, 2]$ one by one. Show lower and upper heap state and median after each insertion.

> [!answer]- Answer Insert 6: lower=[6] upper=[]. Median=6. Insert 3: push to lower=[6,3]. lower.top()=6 > upper is empty so no rebalance needed. sizes: lower=2, upper=0. Move one to upper: lower=[3], upper=[6]. Median=(3+6)/2=4.5. Insert 9: push to lower=[9,3]. lower.top()=9 > upper.top()=6 so move: lower=[3], upper=[6,9]. sizes equal=1,2. Move from upper to lower: lower=[6,3], upper=[9]. Median=6. Insert 2: push to lower=[6,3,2]. lower.top()=6 <= upper.top()=9 ok. sizes: lower=3, upper=1. Move from lower to upper: lower=[3,2], upper=[6,9]. Median=(3+6)/2=4.5.

---

**Q7 — Write it Yourself** Implement a function that returns the k-th largest element in an unsorted array in $O(n \log k)$ time.

> [!answer]- Answer
> 
> ```cpp
> int kthLargest(vector<int>& arr, int k) {
>     // Min-heap of size k
>     priority_queue<int, vector<int>, greater<int>> minPQ;
>     for (int x : arr) {
>         minPQ.push(x);
>         if (minPQ.size() > k) minPQ.pop();
>     }
>     return minPQ.top();  // smallest of top-k = k-th largest
> }
> ```
> 
> The min-heap always holds the k largest elements seen so far. Its top (minimum) is the k-th largest. Each of n elements does at most one push and one pop each costing O(log k) — total O(n log k).

---

**Q8 — Spot the Bug**

```cpp
void heapifyDown(vector<int>& heap, int n, int i) {
    int largest = i;
    int left = 2*i + 1;
    int right = 2*i + 2;
    if (left  < n && heap[left]  > heap[largest]) largest = left;
    if (right < n && heap[right] > heap[largest]) largest = right;
    swap(heap[i], heap[largest]);
    heapifyDown(heap, n, largest);
}
```

> [!answer]- Answer Missing base case check. The function always swaps and always recurses even when i is already the largest — causing unnecessary swaps and infinite recursion when largest == i (a node with no children or already correct). Fix: add if (largest != i) before the swap and recursive call. Without this the recursion never terminates when the heap property is already satisfied at that node.

---

And finally `05_00_Priority_Queues_and_Heaps.md`:

---

# Priority Queues and Heaps

## Notes

- [[05_01_Priority_Queue_Introduction]]
- [[05_02_Binary_Heaps_Min_Max]]
- [[05_03_Insertion_and_Deletion]]
- [[05_04_Heap_Sort]]
- [[05_05_K_Largest_K_Smallest]]
- [[05_06_Median_in_Data_Stream]]

## Quiz

- [[05_QQ_Priority_Queues_Quiz]]

---

## Quick Reference

### Priority Queue ADT

> **Definition.** A priority queue serves elements in order of priority, not arrival. Highest (or lowest) priority element is always at the front.

|Operation|Time|
|---|---|
|insert|$O(\log n)$|
|extractMax / extractMin|$O(\log n)$|
|peek|$O(1)$|

---

### Heap Array Index Formulas (0-indexed)

$$\text{parent}(i) = \lfloor (i-1)/2 \rfloor$$ $$\text{leftChild}(i) = 2i + 1$$ $$\text{rightChild}(i) = 2i + 2$$ $$\text{last internal node} = \lfloor n/2 \rfloor - 1$$ $$\text{height} = \lfloor \log_2 n \rfloor$$

---

### Heap Operations

|Operation|Time|Method|
|---|---|---|
|Insert|$O(\log n)$|Add at end, heapify-up|
|Extract max/min|$O(\log n)$|Swap root with last, heapify-down|
|Peek|$O(1)$|root = heap[0]|
|Build heap|$O(n)$|Heapify-down from $\lfloor n/2 \rfloor - 1$ to 0|
|Heap sort|$O(n \log n)$|Build heap + $n$ extractions|

---

### Min vs Max Heap

||Max-Heap|Min-Heap|
|---|---|---|
|Root|Maximum|Minimum|
|C++ STL|`priority_queue<int>`|`priority_queue<int, vector<int>, greater<int>>`|
|Use for|K largest, Heap Sort|K smallest, Dijkstra|

---

### K Largest / K Smallest

|Approach|Time|Space|Best when|
|---|---|---|---|
|Sort|$O(n \log n)$|$O(1)$|$k \approx n$|
|Heap of size $k$|$O(n \log k)$|$O(k)$|$k \ll n$|
|QuickSelect|$O(n)$ avg|$O(1)$|Single query|

---

### Median in Data Stream

Two heaps — lower max-heap + upper min-heap:

$$\text{median} = \begin{cases} \text{lower.top()} & \text{if } |\text{lower}| > |\text{upper}| \ \text{upper.top()} & \text{if } |\text{upper}| > |\text{lower}| \ \frac{\text{lower.top()} + \text{upper.top()}}{2} & \text{if sizes equal} \end{cases}$$

Insert invariant: lower.top() $\leq$ upper.top() and $\big||\text{lower}| - |\text{upper}|\big| \leq 1$

---
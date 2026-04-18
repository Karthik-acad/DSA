
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

> [!answer]- 
> Answer O(n log n) and Theta(n log n) are both correct. n pushes each costing O(log n) = O(n log n). n pops each costing O(log n) = O(n log n). Total = Theta(n log n). This is actually heap sort — printing elements in decreasing order. O(n) is false — each push/pop involves heapify operations costing O(log n).

---

**Q2 — Array to Heap** Given array $[3, 1, 4, 1, 5, 9, 2, 6]$, draw the max-heap tree and show the array representation after build-heap.

> [!answer]- 
> Answer Build-heap starts from last internal node (index 3, value 1) and heapifies down. After build-heap array: [9, 6, 4, 3, 5, 1, 2, 1] (one valid answer — multiple valid max-heaps exist). Tree: 9 /  
> 6 4 / \ /  
> 3 5 1 2 / 1 Verify: every parent >= both children. Root 9 is maximum. Time O(n).

---

**Q3 — True or False** Building a heap by inserting elements one by one is $O(n \log n)$, but build-heap (heapify from bottom) is $O(n)$.

- [ ] True
- [ ] False

> [!answer]- 
> Answer True. Inserting one by one: each of n insertions costs O(log n) in the worst case = O(n log n) total. Build-heap: most nodes are near the leaves and travel very little distance during heapify-down. The sum n/2 * 0 + n/4 * 1 + n/8 * 2 + ... converges to O(n). The two approaches give the same data structure but with different construction costs.

---

**Q4 — Trace Heap Sort** Trace heap sort on $[4, 10, 3, 5, 1]$. Show the array after build-heap and after each extraction.

> [!answer]- 
> Answer After build-heap: [10, 5, 3, 4, 1]. Extract 1: swap root with last → [1,5,3,4,10], heapify [1,5,3,4] → [5,4,3,1,10]. Extract 2: swap → [1,4,3,5,10] heapify [1,4,3] → [4,1,3,5,10]. Extract 3: swap → [3,1,4,5,10] heapify [3,1] → [3,1,4,5,10]. Extract 4: swap → [1,3,4,5,10] heapify [1] → [1,3,4,5,10]. Final sorted: [1,3,4,5,10].

---

**Q5 — K Largest** Array $[2, 5, 1, 7, 3, 9, 4, 8, 6]$. Find the 4 largest elements using a min-heap of size 4. Trace the heap state as each element is processed.

> [!answer]- 
> Answer Process 2: heap=[2]. Process 5: heap=[2,5]. Process 1: heap=[1,5,2] wait — min-heap so [1,2,5]. Process 7: size=4 heap=[1,2,5,7]. Process 3: 3>min(1) pop 1 push 3: heap=[2,3,5,7]. Process 9: 9>min(2) pop 2 push 9: heap=[3,5,9,7]. Process 4: 4>min(3) pop 3 push 4: heap=[4,5,9,7]. Process 8: 8>min(4) pop 4 push 8: heap=[5,7,9,8]. Process 6: 6>min(5) pop 5 push 6: heap=[6,7,9,8]. Final 4 largest: {6, 7, 8, 9}.

---

**Q6 — Median Stream** Insert the stream $[6, 3, 9, 2]$ one by one. Show lower and upper heap state and median after each insertion.

> [!answer]- 
> Answer Insert 6: lower=[6] upper=[]. Median=6. Insert 3: push to lower=[6,3]. lower.top()=6 > upper is empty so no rebalance needed. sizes: lower=2, upper=0. Move one to upper: lower=[3], upper=[6]. Median=(3+6)/2=4.5. Insert 9: push to lower=[9,3]. lower.top()=9 > upper.top()=6 so move: lower=[3], upper=[6,9]. sizes equal=1,2. Move from upper to lower: lower=[6,3], upper=[9]. Median=6. Insert 2: push to lower=[6,3,2]. lower.top()=6 <= upper.top()=9 ok. sizes: lower=3, upper=1. Move from lower to upper: lower=[3,2], upper=[6,9]. Median=(3+6)/2=4.5.

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

> [!answer]- 
> Answer Missing base case check. The function always swaps and always recurses even when i is already the largest — causing unnecessary swaps and infinite recursion when largest == i (a node with no children or already correct). Fix: add if (largest != i) before the swap and recursive call. Without this the recursion never terminates when the heap property is already satisfied at that node.

---

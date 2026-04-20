
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

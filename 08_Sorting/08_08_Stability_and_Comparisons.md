
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

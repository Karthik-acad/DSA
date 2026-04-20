
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

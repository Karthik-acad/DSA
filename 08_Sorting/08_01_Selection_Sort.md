
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

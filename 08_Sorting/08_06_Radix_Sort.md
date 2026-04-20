
---

# Radix Sort

All the sorting algorithms so far are **comparison-based** — they sort by comparing pairs of elements. There is a theoretical lower bound for comparison-based sorting:

> **Theorem.** Any comparison-based sorting algorithm requires $\Omega(n \log n)$ comparisons in the worst case.

**Radix sort** breaks this barrier by not comparing elements at all — instead it sorts digit by digit. The tradeoff is that it only works on integers (or strings) with a bounded number of digits.

> **Definition.** _Radix sort_ sorts integers by processing digits from least significant to most significant (LSD radix sort), using a stable sort (typically counting sort) at each digit position.

The key requirement — the sort at each digit position must be **stable**. This ensures that previously established order among digits is preserved.

```cpp
#include <iostream>
#include <vector>
using namespace std;

// Counting sort on digit at position exp (1, 10, 100, ...)
// Time: O(n + b) where b = base (10)
void countingByDigit(vector<int>& arr, int exp) {
    int n = arr.size();
    vector<int> output(n), count(10, 0);

    // Count occurrences of each digit
    for (int i = 0; i < n; i++)
        count[(arr[i] / exp) % 10]++;

    // Cumulative count — position in output
    for (int i = 1; i < 10; i++)
        count[i] += count[i-1];

    // Build output array right to left (for stability)
    for (int i = n-1; i >= 0; i--) {
        int digit = (arr[i] / exp) % 10;
        output[--count[digit]] = arr[i];
    }

    arr = output;
}

// Time: O(d * (n + b)) where d = digits, b = base
// Space: O(n + b)
void radixSort(vector<int>& arr) {
    int maxVal = *max_element(arr.begin(), arr.end());
    // Process each digit position
    for (int exp = 1; maxVal / exp > 0; exp *= 10)
        countingByDigit(arr, exp);
}

int main() {
    vector<int> arr = {170, 45, 75, 90, 802, 24, 2, 66};
    radixSort(arr);
    for (int x : arr) cout << x << " ";  // 2 24 45 66 75 90 170 802
    return 0;
}
```

---

## Complexity Analysis

For $n$ numbers each with at most $d$ digits in base $b$:

$$T(n) = O(d \cdot (n + b))$$

If $d$ is constant and $b = O(n)$:

$$T(n) = O(n)$$

This beats the $\Omega(n \log n)$ comparison barrier — but only because we use extra information about the structure of the keys (they are integers with bounded digits).

---

## LSD vs MSD Radix Sort

**LSD (Least Significant Digit)** — process from rightmost digit left. Simpler, works well for fixed-length keys.

**MSD (Most Significant Digit)** — process from leftmost digit right. More complex but can stop early and works for variable-length strings.

---

## Complexity Summary

|Case|Time|Space|
|---|---|---|
|All cases|$O(d(n+b))$|$O(n+b)$|
|Stable|Yes|—|

Best when: keys are integers, $d$ is small, $n$ is large. Not suitable for floating-point numbers or complex comparison keys.

---

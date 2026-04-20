
---

# Binary Search

If the array is sorted, we can do much better than linear scan. **Binary search** exploits sorted order to eliminate half the search space at each step — achieving $O(\log n)$.

> **Definition.** _Binary search_ operates on a sorted array. At each step it compares the target with the middle element and eliminates the half that cannot contain the target, recursing on the remaining half.

Think of finding a word in a dictionary — you open to the middle, see whether your word comes before or after, and eliminate the irrelevant half. You repeat this, each time halving the remaining pages.

```cpp
#include <iostream>
#include <vector>
using namespace std;

// Iterative — Time: O(log n), Space: O(1)
int binarySearch(const vector<int>& arr, int target) {
    int left = 0, right = arr.size() - 1;
    while (left <= right) {
        int mid = left + (right - left) / 2;  // avoids overflow vs (left+right)/2
        if (arr[mid] == target) return mid;
        else if (arr[mid] < target) left = mid + 1;
        else right = mid - 1;
    }
    return -1;
}

// Recursive — Time: O(log n), Space: O(log n)
int binarySearchRec(const vector<int>& arr, int target, int left, int right) {
    if (left > right) return -1;
    int mid = left + (right - left) / 2;
    if (arr[mid] == target) return mid;
    if (arr[mid] < target) return binarySearchRec(arr, target, mid+1, right);
    return binarySearchRec(arr, target, left, mid-1);
}

int main() {
    vector<int> arr = {2, 3, 4, 10, 40};
    cout << binarySearch(arr, 10) << endl;  // 3
    cout << binarySearch(arr, 5)  << endl;  // -1
    return 0;
}
```

---

## Lower Bound and Upper Bound

Two essential variants — finding the first or last occurrence of a target (or the insertion point for a target not in the array).

```cpp
// Lower bound — first index where arr[i] >= target
// C++ equivalent: lower_bound(arr.begin(), arr.end(), target)
int lowerBound(const vector<int>& arr, int target) {
    int left = 0, right = arr.size();  // right = n, not n-1
    while (left < right) {
        int mid = left + (right - left) / 2;
        if (arr[mid] < target) left = mid + 1;
        else right = mid;
    }
    return left;  // first position >= target
}

// Upper bound — first index where arr[i] > target
// C++ equivalent: upper_bound(arr.begin(), arr.end(), target)
int upperBound(const vector<int>& arr, int target) {
    int left = 0, right = arr.size();
    while (left < right) {
        int mid = left + (right - left) / 2;
        if (arr[mid] <= target) left = mid + 1;
        else right = mid;
    }
    return left;  // first position > target
}

// Count occurrences of target using lower and upper bound
int countOccurrences(const vector<int>& arr, int target) {
    return upperBound(arr, target) - lowerBound(arr, target);
}
```

---

## The Overflow Bug

A classic bug:

```cpp
int mid = (left + right) / 2;  // WRONG — can overflow if left + right > INT_MAX
int mid = left + (right - left) / 2;  // CORRECT — no overflow
```

Always use the second form.

---

## Complexity Summary

|Case|Time|Space (iterative)|
|---|---|---|
|Best (found at mid)|$O(1)$|$O(1)$|
|Average|$O(\log n)$|$O(1)$|
|Worst|$O(\log n)$|$O(1)$|

> 📖 For binary search bugs and edge cases: [Nearly All Binary Searches are Broken — Joshua Bloch](https://ai.googleblog.com/2006/06/extra-extra-read-all-about-it-nearly.html)

---

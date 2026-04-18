
---
# Sliding Window

Consider this problem — find the maximum sum of any subarray of length $k$. The naive approach tries every possible subarray of length $k$, each requiring $O(k)$ work to sum — total $O(nk)$.

The **sliding window** technique notices something: when you move from one subarray to the next, you are adding one element and removing one. You do not need to recompute the entire sum — just update it.

Imagine a window of fixed width sliding across the array. At each step it moves one position right — the leftmost element falls out, a new rightmost element comes in. This keeps the cost at $O(1)$ per step.

> **Definition.** The _sliding window_ technique maintains a window (subarray) over an array and slides it one step at a time, updating the result incrementally rather than recomputing from scratch.

There are two variants:

**Fixed size window** — window length $k$ is constant throughout.

**Variable size window** — window expands and contracts based on a condition, used when you are looking for the smallest or largest window satisfying some property.

```cpp
#include <iostream>
#include <vector>
using namespace std;

// Fixed window — maximum sum subarray of length k
// Time: O(n), Space: O(1)
int maxSumFixed(const vector<int>& A, int k) {
    int n = A.size();
    int windowSum = 0;

    // Build first window
    for (int i = 0; i < k; i++)
        windowSum += A[i];

    int maxSum = windowSum;

    // Slide the window
    for (int i = k; i < n; i++) {
        windowSum += A[i];       // add incoming element
        windowSum -= A[i - k];   // remove outgoing element
        maxSum = max(maxSum, windowSum);
    }
    return maxSum;
}

// Variable window — smallest subarray with sum >= target
// Time: O(n), Space: O(1)
int minLenVariable(const vector<int>& A, int target) {
    int n = A.size();
    int left = 0, windowSum = 0;
    int minLen = INT_MAX;

    for (int right = 0; right < n; right++) {
        windowSum += A[right];  // expand window

        while (windowSum >= target) {
            minLen = min(minLen, right - left + 1);
            windowSum -= A[left++];  // contract window
        }
    }
    return minLen == INT_MAX ? 0 : minLen;
}

int main() {
    vector<int> A = {2, 1, 5, 2, 3, 2};
    cout << maxSumFixed(A, 3) << endl;    // 10 (5+2+3)
    cout << minLenVariable(A, 7) << endl; // 2 (5+2)
    return 0;
}
```

---

## Complexity Summary

|Variant|Time|Space|
|---|---|---|
|Fixed window|$O(n)$|$O(1)$|
|Variable window|$O(n)$|$O(1)$|

The variable window is $O(n)$ even though it has a nested `while` — each element is added once and removed at most once, so total operations are $2n$.

>  For a catalogue of sliding window problems: [LeetCode Sliding Window Pattern](https://leetcode.com/tag/sliding-window/)

---

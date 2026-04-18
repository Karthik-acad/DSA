
---

# Kadane's Algorithm — Maximum Subarray

Given an array of integers (possibly negative), find the contiguous subarray with the largest sum. This is called the **maximum subarray problem**.

The brute force tries every possible subarray — $O(n^2)$ or $O(n^3)$ depending on implementation. **Kadane's algorithm** solves it in $O(n)$ with $O(1)$ space using a beautifully simple observation:

At each position $i$, you have exactly two choices:

1. Extend the previous subarray by including $A[i]$
2. Start a fresh subarray beginning at $A[i]$

You pick whichever is larger. If the running sum becomes negative, it is always better to start fresh — a negative prefix can only hurt any future subarray.

> **Definition.** _Kadane's algorithm_ maintains a running maximum subarray sum ending at the current position. At each step it decides whether to extend the current subarray or start a new one, based on which gives a larger sum.

The recurrence is:

$$\text{current}[i] = \max(A[i],\ \text{current}[i-1] + A[i])$$

$$\text{answer} = \max_{i}\ \text{current}[i]$$

```cpp
#include <iostream>
#include <vector>
using namespace std;

// Kadane's Algorithm
// Time: O(n), Space: O(1)
int maxSubarray(const vector<int>& A) {
    int currentSum = A[0];
    int maxSum = A[0];

    for (int i = 1; i < A.size(); i++) {
        // Extend or start fresh
        currentSum = max(A[i], currentSum + A[i]);
        maxSum = max(maxSum, currentSum);
    }
    return maxSum;
}

// Extended version — also returns the subarray indices
pair<int,int> maxSubarrayIndices(const vector<int>& A) {
    int currentSum = A[0], maxSum = A[0];
    int start = 0, end = 0, tempStart = 0;

    for (int i = 1; i < A.size(); i++) {
        if (A[i] > currentSum + A[i]) {
            currentSum = A[i];
            tempStart = i;
        } else {
            currentSum += A[i];
        }
        if (currentSum > maxSum) {
            maxSum = currentSum;
            start = tempStart;
            end = i;
        }
    }
    return {start, end};
}

int main() {
    vector<int> A = {-2, 1, -3, 4, -1, 2, 1, -5, 4};
    cout << maxSubarray(A) << endl;  // 6 (subarray [4,-1,2,1])

    auto [l, r] = maxSubarrayIndices(A);
    cout << "Indices: " << l << " to " << r << endl;  // 3 to 6
    return 0;
}
```

---

## Why it Works — The Intuition

Think of yourself walking along the array accumulating a sum. If at any point your total goes negative, you are only going to drag down whatever comes next. So you reset to zero and start fresh. The global maximum you saw along the entire walk is your answer.

The key insight that makes it $O(n)$ is that you never need to look back — the decision at position $i$ depends only on the best subarray ending at $i-1$, which you already have.

---

## Complexity Summary

|Operation|Time|Space|
|---|---|---|
|Find max sum|$O(n)$|$O(1)$|
|Find max sum + indices|$O(n)$|$O(1)$|

> Original paper by Kadane: referenced in [Programming Pearls by Jon Bentley](https://www.amazon.com/Programming-Pearls-2nd-Jon-Bentley/dp/0201657880) — worth reading for how the algorithm was discovered. 
> For variants (circular array, 2D): [CP Algorithms — Maximum Subarray](https://cp-algorithms.com/others/maximum_average_segment.html)

---

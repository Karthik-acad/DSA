
---

# Two Pointers

Many array problems ask you to find a _pair_ or a _triplet_ of elements satisfying some condition. The brute force uses nested loops — $O(n^2)$ or worse. The **two pointers** technique brings many of these down to $O(n)$ by using two indices that move through the array intelligently.

The core idea — instead of trying every combination, place one pointer at the left end and one at the right end. Based on what you observe at the current pair, move one of them inward. Each pointer moves at most $n$ steps, so the total work is $O(n)$.

This works when the array is **sorted**, because sorting gives you a predictable relationship — moving the left pointer right increases the sum, moving the right pointer left decreases it. You can steer toward your target.

> **Definition.** The _two pointers_ technique uses two indices into an array that move toward each other (or in the same direction) based on a condition, avoiding the need to check all pairs explicitly.

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

// Two Sum — does any pair sum to target?
// Requires sorted array
// Time: O(n), Space: O(1)
bool twoSum(vector<int>& A, int target) {
    int left = 0, right = A.size() - 1;

    while (left < right) {
        int sum = A[left] + A[right];
        if (sum == target) return true;
        else if (sum < target) left++;   // need larger sum
        else right--;                     // need smaller sum
    }
    return false;
}

// Remove duplicates from sorted array in-place
// Time: O(n), Space: O(1)
int removeDuplicates(vector<int>& A) {
    if (A.empty()) return 0;
    int slow = 0;

    for (int fast = 1; fast < A.size(); fast++) {
        if (A[fast] != A[slow]) {
            slow++;
            A[slow] = A[fast];
        }
    }
    return slow + 1;  // new length
}

int main() {
    vector<int> A = {1, 2, 3, 4, 6};
    cout << twoSum(A, 6) << endl;  // 1 (true, pair 2+4)

    vector<int> B = {1, 1, 2, 2, 3};
    cout << removeDuplicates(B) << endl;  // 3
    return 0;
}
```

---

## Two Pointers vs Sliding Window

These two techniques are often confused. The distinction:

| Usage             | Two Pointers                        | Sliding Window                      |
| ----------------- | ----------------------------------- | ----------------------------------- |
| Typical use       | Finding pairs/triplets              | Finding subarrays                   |
| Array requirement | Often needs sorted input            | Works on unsorted                   |
| Pointer movement  | Toward each other or same direction | Always same direction               |
| What you track    | Individual elements                 | Aggregate (sum, count) over a range |

In practice the line can blur — variable sliding window is essentially two pointers moving in the same direction. The mental model differs though.

---

## Complexity Summary

|Operation|Time|Space|
|---|---|---|
|Two sum on sorted array|$O(n)$|$O(1)$|
|Three sum|$O(n^2)$|$O(1)$|
|Remove duplicates|$O(n)$|$O(1)$|

>  For two pointers problem patterns: [LeetCode Two Pointers](https://leetcode.com/tag/two-pointers/)

---

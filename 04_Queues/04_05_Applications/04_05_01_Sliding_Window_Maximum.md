
---

# Sliding Window Maximum

Given an array and a window size $k$, find the maximum element in every contiguous subarray of size $k$.

Example: $[1, 3, -1, -3, 5, 3, 6, 7]$, $k = 3$ → $[3, 3, 5, 5, 6, 7]$

The naive approach checks the maximum of every window — $O(nk)$. The deque-based approach does it in $O(n)$.

**The idea** — maintain a deque of indices such that:

1. The deque only contains indices within the current window
2. Elements in the deque are in decreasing order of their values

This means the front of the deque always holds the index of the maximum element in the current window. When a new element comes in, pop from the back all elements smaller than it — they can never be the maximum while the new element is in the window. When the window slides, pop from the front if the front index has left the window.

> **Definition.** The _sliding window maximum_ uses a monotonic deque (decreasing) to maintain the maximum of the current window, allowing $O(1)$ per step by discarding elements that can never be the window maximum.

```cpp
#include <iostream>
#include <vector>
#include <deque>
using namespace std;

// Time: O(n), Space: O(k)
vector<int> slidingWindowMax(vector<int>& arr, int k) {
    int n = arr.size();
    vector<int> result;
    deque<int> dq;  // stores indices, decreasing order of values

    for (int i = 0; i < n; i++) {
        // Remove indices outside the window
        while (!dq.empty() && dq.front() < i - k + 1)
            dq.pop_front();

        // Remove indices whose values are smaller than current
        // They can never be maximum while arr[i] is in window
        while (!dq.empty() && arr[dq.back()] < arr[i])
            dq.pop_back();

        dq.push_back(i);

        // Window is fully formed from index k-1 onwards
        if (i >= k - 1)
            result.push_back(arr[dq.front()]);
    }
    return result;
}

int main() {
    vector<int> arr = {1, 3, -1, -3, 5, 3, 6, 7};
    vector<int> res = slidingWindowMax(arr, 3);
    for (int x : res) cout << x << " ";  // 3 3 5 5 6 7
    return 0;
}
```

**Why $O(n)$** — each element is added to the deque once and removed at most once, so total deque operations across all $n$ steps is $2n = O(n)$. Same amortized argument as the monotonic stack.

**Complexity:** Time $O(n)$, Space $O(k)$ for the deque.

---

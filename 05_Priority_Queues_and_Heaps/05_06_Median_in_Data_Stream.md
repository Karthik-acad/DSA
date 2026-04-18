
---

# Median in a Data Stream

Given a stream of integers arriving one by one, find the median after each insertion. You cannot sort after every insertion — that would be $O(n^2 \log n)$ total.

This is one of the most elegant heap problems. The key insight — split the data into two halves:

- The **lower half** maintained as a **max-heap** — its root is the largest of the smaller half
- The **upper half** maintained as a **min-heap** — its root is the smallest of the larger half

The median is always at the boundary between the two halves.

> **Definition.** The _two-heap median_ maintains a max-heap for the lower half and a min-heap for the upper half, keeping their sizes equal (or differing by at most 1). The median is the root of the larger heap, or the average of both roots if sizes are equal.

```cpp
#include <iostream>
#include <queue>
using namespace std;

class MedianFinder {
    // Max-heap — lower half
    priority_queue<int> lower;
    // Min-heap — upper half
    priority_queue<int, vector<int>, greater<int>> upper;

public:
    // Time: O(log n)
    void insert(int num) {
        // Step 1 — push to lower half
        lower.push(num);

        // Step 2 — balance: ensure lower.top() <= upper.top()
        if (!upper.empty() && lower.top() > upper.top()) {
            upper.push(lower.top());
            lower.pop();
        }

        // Step 3 — balance sizes: sizes differ by at most 1
        if (lower.size() > upper.size() + 1) {
            upper.push(lower.top());
            lower.pop();
        } else if (upper.size() > lower.size() + 1) {
            lower.push(upper.top());
            upper.pop();
        }
    }

    // Time: O(1)
    double getMedian() {
        if (lower.size() == upper.size())
            return (lower.top() + upper.top()) / 2.0;
        return lower.size() > upper.size() ? lower.top() : upper.top();
    }
};

int main() {
    MedianFinder mf;
    vector<int> stream = {5, 15, 1, 3, 2, 8, 7, 9, 10, 6, 11, 4};

    for (int x : stream) {
        mf.insert(x);
        cout << "Insert " << x << " → median: " << mf.getMedian() << endl;
    }
    return 0;
}
```

---

## Why This Works

At any point, the lower max-heap contains the smallest $\lceil n/2 \rceil$ elements and the upper min-heap contains the largest $\lfloor n/2 \rfloor$ elements. The heap roots are the two middle elements. The median is:

- If sizes equal → average of both roots
- If one is larger → root of the larger heap

Every insertion does at most 2 heap operations — $O(\log n)$ total per insertion.

---

## Complexity Summary

|Operation|Time|Space|
|---|---|---|
|Insert|$O(\log n)$|$O(1)$|
|Get median|$O(1)$|—|
|Total for $n$ insertions|$O(n \log n)$|$O(n)$|

> 📖 For more heap problems: [LeetCode Heap Problems](https://leetcode.com/tag/heap-priority-queue/)

---

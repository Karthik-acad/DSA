
---

# Monotonic Stack

A **monotonic stack** is not a new data structure — it is a regular stack used with a specific discipline: you maintain the invariant that elements in the stack are always in either strictly increasing or strictly decreasing order from bottom to top.

Before pushing a new element, you pop everything that violates the order. This popping step is where the useful work happens — the moment an element gets popped, you have found its answer to some question about its relationship with the new element.

> **Definition.** A _monotonic stack_ is a stack where elements are maintained in monotonically increasing or decreasing order. Elements are popped when they would violate this order, and the act of popping establishes a relationship between the popped element and the element that caused the pop.

The key insight — each element is pushed once and popped at most once, so the total work across all operations is $O(n)$ even though there is a nested loop structure. This is the same amortized argument as the sliding window.

This is the technique that powers all the NGE, NSE, PGE, PSE problems in the Applications folder. The template is always the same — the specific problem determines whether you use an increasing or decreasing stack, and what you record when popping.

---

## The Template

```cpp
// Monotonically decreasing stack template
// Useful for: NGE, Stock Span, Histogram, Rain Water
vector<int> monoDecreasing(vector<int>& arr) {
    int n = arr.size();
    vector<int> result(n, -1);  // default answer
    stack<int> st;  // stores indices, not values

    for (int i = 0; i < n; i++) {
        // Pop all elements smaller than current
        while (!st.empty() && arr[st.top()] < arr[i]) {
            result[st.top()] = arr[i];  // arr[i] is the answer for popped element
            st.pop();
        }
        st.push(i);
    }
    return result;
}

// Monotonically increasing stack template
// Useful for: NSE, PSE
vector<int> monoIncreasing(vector<int>& arr) {
    int n = arr.size();
    vector<int> result(n, -1);
    stack<int> st;

    for (int i = 0; i < n; i++) {
        while (!st.empty() && arr[st.top()] > arr[i]) {
            result[st.top()] = arr[i];
            st.pop();
        }
        st.push(i);
    }
    return result;
}
```

Notice we store **indices** in the stack, not values — this lets us record positions in the result array and also compute distances between elements.

> 📖 For a catalogue of monotonic stack problems: [LeetCode Monotonic Stack](https://leetcode.com/tag/monotonic-stack/)

---

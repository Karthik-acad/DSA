
---

# Naive Pattern Matching

Given a text $T$ of length $n$ and a pattern $P$ of length $m$, find all positions in $T$ where $P$ occurs. The naive approach tries every possible alignment.

> **Definition.** _Naive pattern matching_ slides the pattern across the text one position at a time, checking for a match at each position. It requires $O((n-m+1) \cdot m)$ comparisons in the worst case.

```cpp
#include <iostream>
#include <vector>
#include <string>
using namespace std;

// Time: O((n-m+1)*m) worst, Space: O(1)
vector<int> naiveSearch(const string& text, const string& pattern) {
    int n = text.size(), m = pattern.size();
    vector<int> matches;

    for (int i = 0; i <= n - m; i++) {
        int j = 0;
        while (j < m && text[i+j] == pattern[j]) j++;
        if (j == m) matches.push_back(i);
    }
    return matches;
}

int main() {
    string text = "AABAACAADAABAABA";
    string pat  = "AABA";
    auto pos = naiveSearch(text, pat);
    for (int i : pos) cout << i << " ";  // 0 9 12
    return 0;
}
```

---

## When Naive is $O(nm)$

The worst case is when both text and pattern consist of repeated characters — e.g., text = "AAAAAAA" and pattern = "AAAB". Every position requires checking $m-1$ characters before failing. This motivates KMP.

**Complexity:** $O(nm)$ worst, $O(n)$ best (pattern not found early).

---

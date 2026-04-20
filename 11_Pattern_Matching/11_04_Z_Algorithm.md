
---

# Z Algorithm

The Z algorithm computes, for each position in a string, the length of the longest substring starting from that position that matches a prefix of the string. This single array unlocks efficient pattern matching and many string problems.

> **Definition.** For a string $S$ of length $n$, the _Z array_ $Z[i]$ is the length of the longest substring starting from $S[i]$ that is also a prefix of $S$. $Z[0]$ is undefined (or set to $n$).

For $S$ = "AABXAABAAB":

|Index|0|1|2|3|4|5|6|7|8|9|
|---|---|---|---|---|---|---|---|---|---|---|
|Char|A|A|B|X|A|A|B|A|A|B|
|Z|-|1|0|0|3|1|0|6|1|0|

$Z[7] = 6$ means $S[7..12]$ = "AABXAA"... wait, $S[7..9]$ = "AAB" and that matches $S[0..2]$ = "AAB" — so $Z[7] = 3$.

---

## Computing Z Array

```cpp
#include <iostream>
#include <vector>
#include <string>
using namespace std;

// Time: O(n), Space: O(n)
vector<int> zFunction(const string& s) {
    int n = s.size();
    vector<int> z(n, 0);
    z[0] = n;
    int l = 0, r = 0;  // [l, r) = rightmost Z-box

    for (int i = 1; i < n; i++) {
        if (i < r)
            z[i] = min(r - i, z[i - l]);  // use previously computed info

        while (i + z[i] < n && s[z[i]] == s[i + z[i]])
            z[i]++;

        if (i + z[i] > r) { l = i; r = i + z[i]; }
    }
    return z;
}
```

The [l, r) window is the Z-box — the rightmost interval $[l, r)$ such that $s[l..r-1]$ is a prefix of $s$. When $i$ is inside the Z-box, we can initialize $z[i]$ from $z[i-l]$ to avoid redundant comparisons.

---

## Pattern Matching with Z Algorithm

Concatenate pattern + "$" + text (sentinel "$" ensures no Z value crosses the boundary), compute Z array, find positions where $Z[i] \geq m$.

```cpp
// Time: O(n + m), Space: O(n + m)
vector<int> zSearch(const string& text, const string& pattern) {
    string s = pattern + "$" + text;
    auto z = zFunction(s);
    int m = pattern.size();
    vector<int> matches;

    for (int i = m + 1; i < s.size(); i++)
        if (z[i] >= m)
            matches.push_back(i - m - 1);  // position in original text

    return matches;
}

int main() {
    string text    = "AABAACAADAABAABA";
    string pattern = "AABA";
    auto pos = zSearch(text, pattern);
    for (int i : pos) cout << i << " ";  // 0 9 12
    return 0;
}
```

---

## Z vs KMP

Both achieve $O(n+m)$ pattern matching. The Z algorithm is often considered simpler to implement and reason about. KMP's failure function has a deeper connection to the structure of the pattern, while Z values are more directly interpretable.

**Complexity:** $O(n)$ to build, $O(n+m)$ for pattern matching.

---

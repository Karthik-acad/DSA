
---

# KMP Algorithm

The naive algorithm wastes work — when a mismatch occurs, it discards all information about the characters already matched and starts fresh. **KMP (Knuth-Morris-Pratt)** avoids this by precomputing a **failure function** that tells us how far back to shift the pattern without rechecking already-matched characters.

> **Definition.** The _KMP failure function_ (also called the partial match table or $\pi$ array) for pattern $P$ gives, for each position $i$, the length of the longest proper prefix of $P[0..i]$ that is also a suffix.

> **Definition.** _KMP pattern matching_ uses the failure function to skip redundant comparisons after a mismatch — never moving the text pointer backward.

---

## Computing the Failure Function

```cpp
#include <iostream>
#include <vector>
#include <string>
using namespace std;

// Failure function — O(m) time and space
vector<int> computeFailure(const string& pattern) {
    int m = pattern.size();
    vector<int> fail(m, 0);
    int j = 0;  // length of previous longest prefix-suffix

    for (int i = 1; i < m; i++) {
        while (j > 0 && pattern[i] != pattern[j])
            j = fail[j-1];  // fall back
        if (pattern[i] == pattern[j]) j++;
        fail[i] = j;
    }
    return fail;
}
```

Example: pattern = "AABAAB"

|Index|0|1|2|3|4|5|
|---|---|---|---|---|---|---|
|Char|A|A|B|A|A|B|
|fail|0|1|0|1|2|3|

$\text{fail}[5] = 3$ means "AAB" is both a prefix and suffix of "AABAAB".

---

## KMP Search

```cpp
// Time: O(n + m), Space: O(m)
vector<int> kmpSearch(const string& text, const string& pattern) {
    int n = text.size(), m = pattern.size();
    vector<int> fail = computeFailure(pattern);
    vector<int> matches;
    int j = 0;  // characters matched so far

    for (int i = 0; i < n; i++) {
        while (j > 0 && text[i] != pattern[j])
            j = fail[j-1];  // fall back using failure function
        if (text[i] == pattern[j]) j++;
        if (j == m) {
            matches.push_back(i - m + 1);
            j = fail[j-1];  // look for next match
        }
    }
    return matches;
}

int main() {
    string text    = "AABAACAADAABAABA";
    string pattern = "AABA";
    auto pos = kmpSearch(text, pattern);
    for (int i : pos) cout << i << " ";  // 0 9 12
    return 0;
}
```

---

## Why O(n + m)

The text pointer $i$ never moves backward. The pattern pointer $j$ can only decrease as much as it increases — total increases bounded by $n$, total decreases bounded by $n$. Combined with $O(m)$ preprocessing: $O(n+m)$ total.

**Complexity:** $O(n+m)$ time, $O(m)$ space.

---



---

# Rabin-Karp Algorithm

KMP avoids redundant character comparisons using the failure function. **Rabin-Karp** takes a different approach — use **hashing** to quickly check if a window of the text matches the pattern. If hashes match, verify with a direct comparison.

> **Definition.** _Rabin-Karp_ computes a rolling hash of each window of the text and compares it to the pattern hash. If hashes match, it verifies character by character. The rolling hash updates in $O(1)$ per step.

The rolling hash is what makes this efficient — instead of recomputing the hash from scratch for each new window, subtract the contribution of the outgoing character and add the contribution of the incoming character.

$$h_{\text{new}} = (h_{\text{old}} - \text{text}[i] \cdot p^{m-1}) \cdot p + \text{text}[i+m]$$

```cpp
#include <iostream>
#include <vector>
#include <string>
using namespace std;

// Time: O(n+m) average, O(nm) worst (hash collisions), Space: O(1)
vector<int> rabinKarp(const string& text, const string& pattern) {
    int n = text.size(), m = pattern.size();
    const long long MOD = 1e9 + 7, BASE = 31;
    vector<int> matches;

    // Compute BASE^(m-1) % MOD
    long long highPow = 1;
    for (int i = 0; i < m-1; i++) highPow = highPow * BASE % MOD;

    // Compute pattern hash and first window hash
    long long patHash = 0, winHash = 0;
    for (int i = 0; i < m; i++) {
        patHash = (patHash * BASE + pattern[i]) % MOD;
        winHash = (winHash * BASE + text[i])   % MOD;
    }

    for (int i = 0; i <= n - m; i++) {
        if (winHash == patHash) {
            // Hash match — verify to avoid false positives
            if (text.substr(i, m) == pattern)
                matches.push_back(i);
        }
        // Roll hash — remove text[i], add text[i+m]
        if (i < n - m) {
            winHash = (winHash - text[i] * highPow % MOD + MOD) % MOD;
            winHash = (winHash * BASE + text[i+m]) % MOD;
        }
    }
    return matches;
}

int main() {
    string text = "GEEKS FOR GEEKS";
    string pat  = "GEEKS";
    auto pos = rabinKarp(text, pat);
    for (int i : pos) cout << i << " ";  // 0 10
    return 0;
}
```

---

## Hash Collisions

A **spurious hit** (false positive) is when two different strings have the same hash. The verification step handles this — but if many spurious hits occur, the algorithm degrades to $O(nm)$. Using a large prime modulus makes collisions rare.

**Double hashing** — use two different hash functions. The probability of both giving a false positive simultaneously is negligible.

---

## KMP vs Rabin-Karp

||KMP|Rabin-Karp|
|---|---|---|
|Time|$O(n+m)$ always|$O(n+m)$ average|
|Space|$O(m)$|$O(1)$|
|Multiple patterns|Hard|Easy — hash all patterns|
|2D pattern matching|Hard|Possible|

Rabin-Karp shines for **multiple pattern matching** — hash all patterns into a set, then a single pass through the text checks all patterns simultaneously in $O(n)$ expected time.

---

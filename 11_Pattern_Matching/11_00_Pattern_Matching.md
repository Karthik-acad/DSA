
---

# Pattern Matching

## Notes

- [[11_01_Naive_Pattern_Matching]]
- [[11_02_KMP_Algorithm]]
- [[11_03_Rabin_Karp]]
- [[11_04_Z_Algorithm]]

## Quiz

- [[11_QQ_Pattern_Matching_Quiz]]

---

## Quick Reference

### Algorithm Comparison

|Algorithm|Time|Space|Key idea|
|---|---|---|---|
|Naive|$O(nm)$|$O(1)$|Try every position|
|KMP|$O(n+m)$|$O(m)$|Failure function skips re-comparisons|
|Rabin-Karp|$O(n+m)$ avg|$O(1)$|Rolling hash|
|Z Algorithm|$O(n+m)$|$O(n+m)$|Z array prefix matching|

### KMP Failure Function

$\text{fail}[i]$ = length of longest proper prefix of $P[0..i]$ that is also a suffix.

On mismatch at position $j$ in pattern: set $j = \text{fail}[j-1]$ and retry.

### Rabin-Karp Rolling Hash

$$h_{\text{new}} = ((h_{\text{old}} - T[i] \cdot p^{m-1}) \cdot p + T[i+m]) \mod M$$

### Z Algorithm Pattern Matching

Concatenate $P + $ + T$, find positions where $Z[i] \geq m$.

$$Z[i] \geq m \implies \text{match at position } i - m - 1 \text{ in } T$$

---
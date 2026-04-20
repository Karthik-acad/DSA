
---

## Quiz — Pattern Matching

**Q1 — Naive Worst Case** What is the worst case input for naive pattern matching and why?

> [!answer]- Answer Text = "AAAAAAA...A" (n A's) and pattern = "AAA...AB" (m-1 A's followed by B). At every position i, the algorithm matches m-1 characters before failing on the last one. Total comparisons = (n-m+1)*(m-1) = O(nm). This motivates KMP — which would recognize after the first mismatch that the entire matched prefix "AAA...A" is both a prefix and suffix, avoiding re-scanning.

**Q2 — Failure Function** Compute the KMP failure function for pattern "ABCABCABC".

> [!answer]- Answer Index: 0 1 2 3 4 5 6 7 8 Pattern: A B C A B C A B C fail: 0 0 0 1 2 3 4 5 6 At index 3, 'A' matches pattern[0] so fail[3]=1. At index 4, 'B' matches pattern[1] so fail[4]=2. Continue: fail[5]=3, fail[6]=4 ("ABCA" is prefix=suffix "ABCA" of "ABCABC"), fail[7]=5, fail[8]=6.

**Q3 — KMP Trace** Use KMP to find pattern "ABA" in text "ABABABABAB". Show j values at each step.

> [!answer]- Answer fail for "ABA": [0, 0, 1]. i=0, A==A, j=1. i=1, B==B, j=2. i=2, A==A, j=3=m → match at 0, j=fail[2]=1. i=3, B==B, j=2. i=4, A==A, j=3=m → match at 2, j=fail[2]=1. i=5, B==B, j=2. i=6, A==A, j=3=m → match at 4, j=1. i=7, B==B, j=2. i=8, A==A, j=3=m → match at 6, j=1. Matches at positions 0, 2, 4, 6.

**Q4 — Z Array** Compute the Z array for "AABAABAAB".

> [!answer]- Answer S = A A B A A B A A B (length 9). Z[0] = 9 (whole string). Z[1]: s[1]='A'=s[0], s[2]='B'≠s[1]='A' → Z[1]=1. Z[2]: s[2]='B'≠s[0]='A' → Z[2]=0. Z[3]: s[3..8]="AABAAB", matches prefix "AABAA..." — s[3]='A'=s[0], s[4]='A'=s[1], s[5]='B'=s[2], s[6]='A'=s[3], s[7]='A'=s[4], s[8]='B'=s[5] → Z[3]=6. Z[4]=1 (within Z-box, use Z[1]=1, verify). Z[5]=0. Z[6]=3 (within Z-box, use Z[3] clipped to r-6=3). Z[7]=1. Z[8]=0. Z = [9, 1, 0, 6, 1, 0, 3, 1, 0].

**Q5 — Rabin-Karp** Why does Rabin-Karp degrade to O(nm) and how do you prevent it?

> [!answer]- Answer Rabin-Karp degrades when there are many spurious hits — positions where the hash matches but the characters don't. In the worst case (text = "AAAA...A", pattern = "AAA...A" with a different last char but same hash) every position is a hash match requiring O(m) verification. Total = O(nm). Prevention: use double hashing (two independent hash functions). A spurious hit in both simultaneously has probability ~1/MOD^2 ≈ 10^-18 — negligible. Also choose a large prime modulus and a good base.

**Q6 — Write it Yourself** Implement the Z algorithm from scratch and use it to count how many times pattern "ab" appears in text "ababab".

> [!answer]- Answer s = "ab$ababab". Z array computation: Z[0]=8 (length). Z[1]: 'b'≠'a' → 0. Z[2]: '$'≠'a' → 0. Z[3]: 'a'='a','b'='b','$'≠'a' → Z[3]=2=m → match at 3-3=0. Z[4]: use Z-box, Z[4]=1 (b≠a). Z[5]: 'a','b','a'... Z[5]=4? No — check s[5]='a'=s[0], s[6]='b'=s[1], s[7]='a'=s[2]='$'? No s[2]='$'≠s[7]='a'. Z[5]=2 → match at 5-3=2. Z[6]=1. Z[7]=2 → match at 7-3=4. Matches at positions 0, 2, 4 — 3 occurrences.

---

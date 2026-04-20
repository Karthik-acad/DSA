
---

## Quiz — Dictionaries

**Q1 — Identify the Complexity**

```cpp
unordered_map<int,int> mp;
for (int x : arr) mp[x]++;
for (auto& [k,v] : mp) cout << k << " " << v;
```

- [ ] $O(n)$
- [ ] $O(n \log n)$
- [ ] $\Theta(n)$
- [ ] $O(n^2)$

> [!answer]- Answer O(n) and Theta(n) are both correct. n insertions into unordered_map each O(1) average = O(n). Iterating over all entries = O(n). Total Theta(n) average. O(n^2) worst case is theoretically possible if all keys hash to same slot but practically never happens with a good hash function.

---

**Q2 — Hash Function** Which of the following is a bad hash function for string keys and why?

```cpp
int h1(string s, int m) { return s.size() % m; }
int h2(string s, int m) { int sum=0; for(char c:s) sum+=c; return sum%m; }
int h3(string s, int m) { long long h=0,p=31; for(char c:s){h=h*p+(c-'a'+1);} return h%m; }
```

> [!answer]- Answer h1 is worst — maps all strings of the same length to the same slot. All 3-letter words collide, all 4-letter words collide, etc. Catastrophic clustering. h2 is bad — "abc" and "bca" and "cab" all hash identically since addition is commutative. Order of characters is ignored. Many real-world strings are anagrams of each other. h3 is good — polynomial rolling hash. Each character's position affects the result due to multiplication by p^i. Small changes in input cause large changes in output.

---

**Q3 — Collision Resolution** A hash table of size 7 uses linear probing with $h(k) = k \mod 7$. Insert keys 50, 700, 76, 85, 92, 73, 101 in order. Show the final table state.

> [!answer]- Answer h(50)=1, h(700)=0, h(76)=6, h(85)=1→collision→2, h(92)=1→1 occupied→2 occupied→3, h(73)=3→collision→4, h(101)=3→3 occupied→4 occupied→5. Table: [700, 50, 85, 92, 73, 101, 76] Indices: [0, 1, 2, 3, 4, 5, 6] Notice the cluster forming around index 1-5 — primary clustering in action.

---

**Q4 — Subarray Sum** Array $[3, 4, 7, 2, -3, 1, 4, 2]$, target = 7. How many subarrays sum to 7? Trace the prefix sum hash map.

> [!answer]- Answer Prefix sums: 0,3,7,14,16,13,14,18,20. At each step check if prefixSum - 7 exists in map: sum=3: 3-7=-4 not seen. map={0:1,3:1}. sum=7: 7-7=0 seen once → count=1. map={0:1,3:1,7:1}. sum=14: 14-7=7 seen once → count=2. map adds 14. sum=16: 16-7=9 not seen. sum=13: 13-7=6 not seen. sum=14: 14-7=7 seen → count=3. map[14] becomes 2. sum=18: 18-7=11 not seen. sum=20: 20-7=13 seen → count=4. Answer: 4 subarrays.

---

**Q5 — True or False** Double hashing has no clustering problems and always achieves $O(1)$ average operations regardless of load factor.

- [ ] True
- [ ] False

> [!answer]- Answer False on the second part. Double hashing eliminates clustering (true) but performance still degrades as load factor approaches 1. At alpha=0.99, expected probes approaches 100. The O(1) average only holds when alpha is bounded by a constant below 1. At high load factors even double hashing becomes slow — this is why rehashing at alpha=0.75 is standard practice.

---

**Q6 — Write it Yourself** Given an array of integers, find the length of the longest consecutive sequence. Example: [100,4,200,1,3,2] → 4 (sequence 1,2,3,4). Must be O(n).

> [!answer]- Answer
> 
> ```cpp
> int longestConsecutive(vector<int>& arr) {
>     unordered_set<int> s(arr.begin(), arr.end());
>     int maxLen = 0;
>     for (int x : s) {
>         if (!s.count(x-1)) {  // x is start of a sequence
>             int len = 1;
>             while (s.count(x+len)) len++;
>             maxLen = max(maxLen, len);
>         }
>     }
>     return maxLen;
> }
> ```
> 
> Key insight: only start counting from the beginning of a sequence (x-1 not in set). This ensures each sequence is counted once. Each element is visited at most twice total across all sequences — O(n) overall.

---

**Q7 — Anagram Groups** Group these words into anagram families: [listen, silent, enlist, inlets, hello, world, dlrow].

> [!answer]- Answer Sort each word to get the key: listen→eilnst, silent→eilnst, enlist→eilnst, inlets→eilnst → all one group. hello→ehllo. world→dlorw, dlrow→dlorw → one group. Groups: [listen, silent, enlist, inlets], [hello], [world, dlrow].

---

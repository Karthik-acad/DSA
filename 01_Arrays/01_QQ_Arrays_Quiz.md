
## Quiz — Arrays

**Q1 — Identify the Complexity**

```cpp
cout << arr[n-1];
```

Which are true?

- [ ] $O(1)$
- [ ] $\Theta(1)$
- [ ] $O(n)$
- [ ] $o(n)$

> [!answer]- 
> Answer O(1), Theta(1), and o(n) are all correct. Array index access is always constant time regardless of n — the address is computed directly as base + index * size. o(n) holds because O(1) grows strictly slower than O(n).

---

**Q2 — Identify the Complexity** What happens internally when you push_back to a vector that is already at full capacity?

- [ ] $O(1)$
- [ ] $O(n)$
- [ ] $O(\log n)$
- [ ] Amortized $O(1)$

> [!answer]- 
> Answer O(n) for that single operation (copies all n elements to new array) but amortized O(1) over a sequence of push_backs. The doubling strategy ensures that by the time you copy n elements, you have done n cheap insertions to get there — so the average cost per operation is O(1). Both O(n) and amortized O(1) are correct depending on what you are measuring.

---

**Q3 — Trace the Output**

```cpp
vector<int> A = {1, 2, 3, 4, 5};
vector<int> P(5);
P[0] = A[0];
for (int i = 1; i < 5; i++)
    P[i] = P[i-1] + A[i];
cout << P[4] - P[1];
```

> [!answer]-  
> 
> Answer P = {1, 3, 6, 10, 15}. P[4] - P[1] = 15 - 3 = 12. This is the sum of A[2] + A[3] + A[4] = 3 +  4 + 5 = 12. This is exactly how prefix sum range queries work for l=2, r=4.

---

**Q4 — Find the Complexity**

```cpp
int maxSum = INT_MIN;
for (int i = 0; i < n; i++)
    for (int j = i; j < n; j++) {
        int sum = 0;
        for (int k = i; k <= j; k++)
            sum += arr[k];
        maxSum = max(maxSum, sum);
    }
```

What is the time complexity? Can you do better? How?

> [!answer]- 
> 
> This is O(n^3) — three nested loops. You can do better two ways: Prefix sums bring it to O(n^2) — precompute P in O(n), then each subarray sum is O(1), so trying all O(n^2) subarrays costs O(n^2) total. Kadane's algorithm brings it all the way to O(n) — you do not need to try all subarrays explicitly, just track the best ending at each position.

---

**Q5 — Sliding Window** Given array {2, 3, 1, 2, 4, 3} and target = 7, what is the length of the smallest subarray with sum >= 7? Trace through the variable window algorithm by hand.

> [!answer]- 
> 
> Answer 
> is 2 (subarray [4,3]). Trace: right=0 sum=2, right=1 sum=5, right=2 sum=6, right=3 sum=8 >= 7 so minLen=4, shrink left: sum=6 < 7 stop. right=4 sum=10 >= 7 minLen=3, shrink: sum=7 >= 7 minLen=2, shrink: sum=3 < 7 stop. right=5 sum=6 < 7. Final answer: 2.

---

**Q6 — Two Pointers** Array {1, 3, 4, 5, 7, 10, 11} — does any pair sum to 15? Trace the two pointer approach.

> [!answer]- 
> Answer Yes, pair (4, 11) and pair (5, 10). Trace: left=1,right=11 sum=12 < 15 move left. left=3,right=11 sum=14 < 15 move left. left=4,right=11 sum=15 == 15 found. Continue: left=4,right=10 sum=14 move left. left=5,right=10 sum=15 == 15 found. Continue until left >= right.

---

**Q7 — Write it Yourself** Given a sorted array, remove duplicates in-place and return the new length. You cannot use extra space.

> [!answer]- Answer
> 
> ```cpp
> int removeDuplicates(vector<int>& A) {
>     if (A.empty()) return 0;
>     int slow = 0;
>     for (int fast = 1; fast < A.size(); fast++)
>         if (A[fast] != A[slow])
>             A[++slow] = A[fast];
>     return slow + 1;
> }
> ```
> 
> Time: O(n), Space: O(1). The slow pointer marks the boundary of the deduplicated section. Fast scans ahead and whenever it finds a new unique element, slow advances and that element is written in.

---

**Q8 — Kadane's** Trace Kadane's algorithm on {-2, 1, -3, 4, -1, 2, 1, -5, 4}. What is the maximum subarray sum and which subarray gives it?

> [!answer]- 
> Answer Trace currentSum and maxSum at each step: i=0: current=-2, max=-2 i=1: current=max(1, -2+1)=1, max=1 i=2: current=max(-3, 1-3)=-2, max=1 i=3: current=max(4, -2+4)=4, max=4 i=4: current=max(-1, 4-1)=3, max=4 i=5: current=max(2, 3+2)=5, max=5 i=6: current=max(1, 5+1)=6, max=6 i=7: current=max(-5, 6-5)=1, max=6 i=8: current=max(4, 1+4)=5, max=6 Answer: 6, subarray [4, -1, 2, 1] at indices 3 to 6.

---

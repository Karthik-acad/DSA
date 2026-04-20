Starting with Dynamic Programming.

Here is `12_01_Memoization_vs_Tabulation.md`:

---

# Memoization vs Tabulation

From our study of recursion in [[00_06_Recursion_and_Recurrences]], we know that naive recursive solutions often recompute the same subproblems repeatedly. **Dynamic programming** solves this by storing the results of subproblems and reusing them.

Consider Fibonacci — the naive recursive solution computes $fib(5)$ by computing $fib(4)$ and $fib(3)$, but $fib(3)$ is also computed inside $fib(4)$. The call tree has exponential size $O(2^n)$ despite only $O(n)$ distinct subproblems existing.

> **Definition.** _Dynamic programming_ is an algorithmic technique that solves problems by breaking them into overlapping subproblems, solving each subproblem exactly once, and storing the results for reuse. It applies when a problem has **optimal substructure** and **overlapping subproblems**.

> **Definition.** _Optimal substructure_ means the optimal solution to a problem can be constructed from optimal solutions to its subproblems.

> **Definition.** _Overlapping subproblems_ means the same subproblems are encountered multiple times during the recursive computation.

---

## Two Approaches

### Memoization — Top Down

Write the natural recursion, but before returning, store the result. Before computing, check if it is already stored.

```cpp
#include <iostream>
#include <vector>
using namespace std;

// Fibonacci — Top Down with Memoization
// Time: O(n), Space: O(n) memo + O(n) call stack
vector<int> memo;

int fib(int n) {
    if (n <= 1) return n;
    if (memo[n] != -1) return memo[n];  // already computed
    return memo[n] = fib(n-1) + fib(n-2);
}

int main() {
    int n = 10;
    memo.assign(n+1, -1);
    cout << fib(n) << endl;  // 55
    return 0;
}
```

The call tree is now a DAG — each subproblem computed once, all others looked up in $O(1)$.

---

### Tabulation — Bottom Up

Build the solution iteratively from the smallest subproblems up. No recursion, no call stack overhead.

```cpp
// Fibonacci — Bottom Up Tabulation
// Time: O(n), Space: O(n) — can reduce to O(1)
int fibTab(int n) {
    if (n <= 1) return n;
    vector<int> dp(n+1);
    dp[0] = 0; dp[1] = 1;
    for (int i = 2; i <= n; i++)
        dp[i] = dp[i-1] + dp[i-2];
    return dp[n];
}

// Space optimized — O(1)
int fibOptimal(int n) {
    if (n <= 1) return n;
    int prev2 = 0, prev1 = 1;
    for (int i = 2; i <= n; i++) {
        int curr = prev1 + prev2;
        prev2 = prev1;
        prev1 = curr;
    }
    return prev1;
}
```

---

## Comparison

||Memoization (Top Down)|Tabulation (Bottom Up)|
|---|---|---|
|Approach|Recursive + cache|Iterative table|
|Call stack|$O(n)$ recursion depth|$O(1)$|
|Subproblems|Only computed ones|All subproblems|
|Code style|Closer to natural recursion|More explicit ordering|
|Cache|Hash map or array|Array|
|Easier when|Subproblem structure complex|Dependency order clear|

---

## The DP Design Process

Every DP problem follows the same four steps:

1. **Define the subproblem** — what does $dp[i]$ (or $dp[i][j]$) represent?
2. **Write the recurrence** — how does $dp[i]$ depend on smaller subproblems?
3. **Identify base cases** — what are the trivially known values?
4. **Determine evaluation order** — ensure dependencies are computed before they are needed

Getting step 1 right is the hardest part. The rest follows.

> 📖 For DP patterns and problem sets: [Atcoder DP Contest](https://atcoder.jp/contests/dp)

---

Now the 1D DP subfolder. Here is `12_02_01_Fibonacci.md`:

---

# Fibonacci

Already covered in [[12_01_Memoization_vs_Tabulation]] as the introductory example. This note extends it with the matrix exponentiation method for $O(\log n)$.

> **Definition.** The _Fibonacci sequence_ is defined by $F(0) = 0$, $F(1) = 1$, $F(n) = F(n-1) + F(n-2)$ for $n \geq 2$.

---

## Matrix Exponentiation — O(log n)

$$\begin{pmatrix} F(n+1) \ F(n) \end{pmatrix} = \begin{pmatrix} 1 & 1 \ 1 & 0 \end{pmatrix}^n \begin{pmatrix} 1 \ 0 \end{pmatrix}$$

Using fast matrix exponentiation (squaring), we compute $F(n)$ in $O(\log n)$ — useful for very large $n$.

```cpp
#include <iostream>
#include <vector>
using namespace std;

typedef vector<vector<long long>> Matrix;

Matrix multiply(const Matrix& A, const Matrix& B) {
    int n = A.size();
    Matrix C(n, vector<long long>(n, 0));
    for (int i = 0; i < n; i++)
        for (int j = 0; j < n; j++)
            for (int k = 0; k < n; k++)
                C[i][j] += A[i][k] * B[k][j];
    return C;
}

Matrix matPow(Matrix M, long long p) {
    int n = M.size();
    Matrix result(n, vector<long long>(n, 0));
    for (int i = 0; i < n; i++) result[i][i] = 1;  // identity
    while (p > 0) {
        if (p & 1) result = multiply(result, M);
        M = multiply(M, M);
        p >>= 1;
    }
    return result;
}

// Time: O(log n), Space: O(1)
long long fib(long long n) {
    if (n <= 1) return n;
    Matrix M = {{1,1},{1,0}};
    Matrix res = matPow(M, n);
    return res[0][1];
}

int main() {
    cout << fib(10)  << endl;  // 55
    cout << fib(50)  << endl;  // 12586269025
    return 0;
}
```

|Method|Time|Space|
|---|---|---|
|Naive recursion|$O(2^n)$|$O(n)$|
|Memoization|$O(n)$|$O(n)$|
|Tabulation|$O(n)$|$O(n)$|
|Space optimized|$O(n)$|$O(1)$|
|Matrix exponentiation|$O(\log n)$|$O(1)$|

---

Here is `12_02_02_Climbing_Stairs.md`:

---

# Climbing Stairs

You can climb 1 or 2 stairs at a time. How many distinct ways can you reach stair $n$?

This is Fibonacci in disguise — $ways(n) = ways(n-1) + ways(n-2)$. To reach stair $n$, you either came from $n-1$ (took 1 step) or from $n-2$ (took 2 steps).

> **Subproblem:** $dp[i]$ = number of distinct ways to reach stair $i$. **Recurrence:** $dp[i] = dp[i-1] + dp[i-2]$ **Base cases:** $dp[1] = 1$, $dp[2] = 2$

```cpp
#include <iostream>
#include <vector>
using namespace std;

// Time: O(n), Space: O(1)
int climbStairs(int n) {
    if (n <= 2) return n;
    int prev2 = 1, prev1 = 2;
    for (int i = 3; i <= n; i++) {
        int curr = prev1 + prev2;
        prev2 = prev1;
        prev1 = curr;
    }
    return prev1;
}

// Generalization — can climb 1 to k steps at a time
int climbStairsK(int n, int k) {
    vector<int> dp(n+1, 0);
    dp[0] = 1;
    for (int i = 1; i <= n; i++)
        for (int j = 1; j <= k && j <= i; j++)
            dp[i] += dp[i-j];
    return dp[n];
}

int main() {
    cout << climbStairs(5) << endl;     // 8
    cout << climbStairsK(5, 3) << endl; // 13 (can take 1,2,3 steps)
    return 0;
}
```

**Complexity:** $O(n)$ time, $O(1)$ space.

---

Here is `12_02_03_House_Robber.md`:

---

# House Robber

A robber wants to rob houses along a street. Adjacent houses have security systems — robbing two adjacent houses triggers an alarm. Given house values, maximize the total robbed amount.

> **Subproblem:** $dp[i]$ = maximum money robbed from houses $0$ through $i$. **Recurrence:** $dp[i] = \max(dp[i-1],\ dp[i-2] + \text{nums}[i])$ Either skip house $i$ (take $dp[i-1]$) or rob it (take $dp[i-2] + nums[i]$). **Base cases:** $dp[0] = nums[0]$, $dp[1] = \max(nums[0], nums[1])$

```cpp
#include <iostream>
#include <vector>
using namespace std;

// Time: O(n), Space: O(1)
int rob(vector<int>& nums) {
    int n = nums.size();
    if (n == 1) return nums[0];
    int prev2 = nums[0];
    int prev1 = max(nums[0], nums[1]);
    for (int i = 2; i < n; i++) {
        int curr = max(prev1, prev2 + nums[i]);
        prev2 = prev1;
        prev1 = curr;
    }
    return prev1;
}

// Circular houses — first and last are adjacent
// Rob either houses [0..n-2] or [1..n-1]
int robCircular(vector<int>& nums) {
    int n = nums.size();
    if (n == 1) return nums[0];
    vector<int> a(nums.begin(), nums.end()-1);
    vector<int> b(nums.begin()+1, nums.end());
    return max(rob(a), rob(b));
}

int main() {
    vector<int> nums = {2, 7, 9, 3, 1};
    cout << rob(nums) << endl;         // 12 (rob 2+9+1)
    vector<int> circ = {2, 3, 2};
    cout << robCircular(circ) << endl; // 3
    return 0;
}
```

**Complexity:** $O(n)$ time, $O(1)$ space.

---

Here is `12_02_04_Coin_Change.md`:

---

# Coin Change

Given coin denominations and a target amount, find the minimum number of coins to make the amount (coins can be reused).

> **Subproblem:** $dp[i]$ = minimum coins to make amount $i$. **Recurrence:** $dp[i] = \min_{c \in \text{coins}}(dp[i-c] + 1)$ for all $c \leq i$ **Base cases:** $dp[0] = 0$, $dp[i] = \infty$ initially

```cpp
#include <iostream>
#include <vector>
#include <climits>
using namespace std;

// Time: O(amount * coins), Space: O(amount)
int coinChange(vector<int>& coins, int amount) {
    vector<int> dp(amount+1, INT_MAX);
    dp[0] = 0;

    for (int i = 1; i <= amount; i++)
        for (int c : coins)
            if (c <= i && dp[i-c] != INT_MAX)
                dp[i] = min(dp[i], dp[i-c] + 1);

    return dp[amount] == INT_MAX ? -1 : dp[amount];
}

// Count number of ways to make amount (unbounded knapsack variant)
int coinWays(vector<int>& coins, int amount) {
    vector<long long> dp(amount+1, 0);
    dp[0] = 1;
    for (int c : coins)          // outer loop on coins for combinations
        for (int i = c; i <= amount; i++)
            dp[i] += dp[i-c];
    return dp[amount];
}

int main() {
    vector<int> coins = {1, 5, 6, 9};
    cout << coinChange(coins, 11) << endl;  // 2 (5+6)
    cout << coinWays(coins, 4)   << endl;  // 1 (1+1+1+1)
    return 0;
}
```

**Note on loop order for counting ways:** Outer loop on coins, inner on amounts — counts combinations (order doesn't matter). Swap loops for permutations (order matters).

**Complexity:** $O(\text{amount} \times |\text{coins}|)$ time, $O(\text{amount})$ space.

---

Now the 2D DP subfolder. Here is `12_03_01_LCS_Longest_Common_Subsequence.md`:

---

# LCS — Longest Common Subsequence

Given two strings $X$ and $Y$, find the length of their longest common subsequence — the longest sequence of characters that appears in both strings in the same order (not necessarily contiguous).

Example: LCS("ABCBDAB", "BDCAB") = "BCAB" or "BDAB", length 4.

> **Subproblem:** $dp[i][j]$ = length of LCS of $X[0..i-1]$ and $Y[0..j-1]$. **Recurrence:** $$dp[i][j] = \begin{cases} dp[i-1][j-1] + 1 & \text{if } X[i-1] = Y[j-1] \ \max(dp[i-1][j],\ dp[i][j-1]) & \text{otherwise} \end{cases}$$ **Base cases:** $dp[0][j] = dp[i][0] = 0$ — LCS with empty string is 0.

```cpp
#include <iostream>
#include <vector>
#include <string>
using namespace std;

// Time: O(mn), Space: O(mn) — reducible to O(min(m,n))
int lcs(const string& X, const string& Y) {
    int m = X.size(), n = Y.size();
    vector<vector<int>> dp(m+1, vector<int>(n+1, 0));

    for (int i = 1; i <= m; i++)
        for (int j = 1; j <= n; j++)
            if (X[i-1] == Y[j-1]) dp[i][j] = dp[i-1][j-1] + 1;
            else dp[i][j] = max(dp[i-1][j], dp[i][j-1]);

    return dp[m][n];
}

// Reconstruct the actual LCS string
string lcsString(const string& X, const string& Y) {
    int m = X.size(), n = Y.size();
    vector<vector<int>> dp(m+1, vector<int>(n+1, 0));

    for (int i = 1; i <= m; i++)
        for (int j = 1; j <= n; j++)
            if (X[i-1] == Y[j-1]) dp[i][j] = dp[i-1][j-1] + 1;
            else dp[i][j] = max(dp[i-1][j], dp[i][j-1]);

    // Backtrack
    string result;
    int i = m, j = n;
    while (i > 0 && j > 0) {
        if (X[i-1] == Y[j-1]) { result += X[i-1]; i--; j--; }
        else if (dp[i-1][j] > dp[i][j-1]) i--;
        else j--;
    }
    reverse(result.begin(), result.end());
    return result;
}

int main() {
    string X = "ABCBDAB", Y = "BDCAB";
    cout << lcs(X, Y) << endl;        // 4
    cout << lcsString(X, Y) << endl;  // BCAB or BDAB
    return 0;
}
```

**Complexity:** $O(mn)$ time, $O(mn)$ space — reducible to $O(\min(m,n))$ by keeping only two rows.

---

Here is `12_03_02_LIS_Longest_Increasing_Subsequence.md`:

---

# LIS — Longest Increasing Subsequence

Given an array, find the length of the longest strictly increasing subsequence (elements need not be contiguous).

Example: LIS of $[10, 9, 2, 5, 3, 7, 101, 18]$ = 4 (e.g., $[2, 3, 7, 18]$).

---

## $O(n^2)$ DP

> **Subproblem:** $dp[i]$ = length of LIS ending at index $i$. **Recurrence:** $dp[i] = 1 + \max_{j < i,\ arr[j] < arr[i]} dp[j]$ **Base cases:** $dp[i] = 1$ (each element is an LIS of length 1 by itself)

```cpp
// Time: O(n^2), Space: O(n)
int lisDP(vector<int>& arr) {
    int n = arr.size();
    vector<int> dp(n, 1);
    int maxLen = 1;

    for (int i = 1; i < n; i++) {
        for (int j = 0; j < i; j++)
            if (arr[j] < arr[i])
                dp[i] = max(dp[i], dp[j] + 1);
        maxLen = max(maxLen, dp[i]);
    }
    return maxLen;
}
```

---

## $O(n \log n)$ Patience Sorting

Maintain a `tails` array where `tails[i]` is the smallest tail element of all increasing subsequences of length $i+1$. This array is always sorted, allowing binary search.

```cpp
#include <algorithm>

// Time: O(n log n), Space: O(n)
int lisFast(vector<int>& arr) {
    vector<int> tails;
    for (int x : arr) {
        auto it = lower_bound(tails.begin(), tails.end(), x);
        if (it == tails.end()) tails.push_back(x);
        else *it = x;
    }
    return tails.size();
}

int main() {
    vector<int> arr = {10, 9, 2, 5, 3, 7, 101, 18};
    cout << lisDP(arr)   << endl;  // 4
    cout << lisFast(arr) << endl;  // 4
    return 0;
}
```

**Why it works:** `tails` is always sorted. `lower_bound` finds where $x$ fits — if it extends the longest sequence, append; otherwise replace to keep tails as small as possible (giving future elements more room to extend).

**Complexity:** $O(n \log n)$ time, $O(n)$ space.

---

Here is `12_03_03_0_1_Knapsack.md`:

---

# 0-1 Knapsack

Given $n$ items each with a weight $w_i$ and value $v_i$, and a knapsack of capacity $W$, maximize the total value of items that fit. Each item can be taken at most once (0 or 1 times).

> **Subproblem:** $dp[i][j]$ = maximum value using items $0..i-1$ with capacity $j$. **Recurrence:** $$dp[i][j] = \begin{cases} dp[i-1][j] & \text{if } w_i > j \text{ (can't fit item } i\text{)} \ \max(dp[i-1][j],\ dp[i-1][j-w_i] + v_i) & \text{otherwise} \end{cases}$$ **Base cases:** $dp[0][j] = 0$ — no items, zero value.

```cpp
#include <iostream>
#include <vector>
using namespace std;

// Time: O(nW), Space: O(nW) — reducible to O(W)
int knapsack(vector<int>& weights, vector<int>& values, int W) {
    int n = weights.size();
    vector<vector<int>> dp(n+1, vector<int>(W+1, 0));

    for (int i = 1; i <= n; i++)
        for (int j = 0; j <= W; j++) {
            dp[i][j] = dp[i-1][j];  // don't take item i
            if (weights[i-1] <= j)
                dp[i][j] = max(dp[i][j], dp[i-1][j-weights[i-1]] + values[i-1]);
        }
    return dp[n][W];
}

// Space optimized — O(W) using 1D array
// IMPORTANT: iterate j from W down to w[i] to avoid using item twice
int knapsack1D(vector<int>& weights, vector<int>& values, int W) {
    int n = weights.size();
    vector<int> dp(W+1, 0);
    for (int i = 0; i < n; i++)
        for (int j = W; j >= weights[i]; j--)  // reverse to avoid reuse
            dp[j] = max(dp[j], dp[j-weights[i]] + values[i]);
    return dp[W];
}

int main() {
    vector<int> w = {2, 3, 4, 5}, v = {3, 4, 5, 6};
    cout << knapsack(w, v, 5)   << endl;  // 7 (items 0+1)
    cout << knapsack1D(w, v, 5) << endl;  // 7
    return 0;
}
```

**Key insight on loop direction:** In the space-optimized version, iterating $j$ from high to low ensures each item is used at most once — when computing $dp[j]$, $dp[j - w_i]$ still holds the value from the previous item's row. If you iterate low to high (as in coin change), an item can be used multiple times (unbounded knapsack).

**Complexity:** $O(nW)$ time, $O(W)$ space with optimization.

---

Here is `12_03_04_Edit_Distance.md`:

---

# Edit Distance

Given two strings $X$ and $Y$, find the minimum number of operations (insert, delete, replace) to transform $X$ into $Y$. This is the **Levenshtein distance**.

> **Subproblem:** $dp[i][j]$ = minimum operations to transform $X[0..i-1]$ into $Y[0..j-1]$. **Recurrence:** $$dp[i][j] = \begin{cases} dp[i-1][j-1] & \text{if } X[i-1] = Y[j-1] \text{ (no operation needed)} \ 1 + \min(dp[i-1][j],\ dp[i][j-1],\ dp[i-1][j-1]) & \text{otherwise} \end{cases}$$ The three options: delete from $X$ ($dp[i-1][j]$), insert into $X$ ($dp[i][j-1]$), replace ($dp[i-1][j-1]$). **Base cases:** $dp[i][0] = i$ (delete all), $dp[0][j] = j$ (insert all).

```cpp
#include <iostream>
#include <vector>
#include <string>
using namespace std;

// Time: O(mn), Space: O(mn) — reducible to O(min(m,n))
int editDistance(const string& X, const string& Y) {
    int m = X.size(), n = Y.size();
    vector<vector<int>> dp(m+1, vector<int>(n+1));

    for (int i = 0; i <= m; i++) dp[i][0] = i;
    for (int j = 0; j <= n; j++) dp[0][j] = j;

    for (int i = 1; i <= m; i++)
        for (int j = 1; j <= n; j++) {
            if (X[i-1] == Y[j-1]) dp[i][j] = dp[i-1][j-1];
            else dp[i][j] = 1 + min({dp[i-1][j], dp[i][j-1], dp[i-1][j-1]});
        }
    return dp[m][n];
}

int main() {
    cout << editDistance("sunday", "saturday") << endl;  // 3
    cout << editDistance("kitten", "sitting")  << endl;  // 3
    return 0;
}
```

**Applications:** Spell checkers, DNA sequence alignment, diff tools (git diff), plagiarism detection.

**Complexity:** $O(mn)$ time, $O(mn)$ space — reducible to $O(\min(m,n))$.

---

Here is `12_03_05_Matrix_Chain_Multiplication.md`:

---

# Matrix Chain Multiplication

Given a sequence of matrices $A_1, A_2, \ldots, A_n$, find the optimal parenthesization that minimizes the total number of scalar multiplications. Matrix multiplication is associative — different parenthesizations give the same result but different costs.

For matrices with dimensions $p \times q$ and $q \times r$, the multiplication costs $p \times q \times r$ scalar multiplications.

> **Subproblem:** $dp[i][j]$ = minimum cost to multiply matrices $A_i$ through $A_j$. **Recurrence:** Split at some $k$ between $i$ and $j$: $$dp[i][j] = \min_{i \leq k < j}(dp[i][k] + dp[k+1][j] + p_i \times p_{k+1} \times p_{j+1})$$ where $p$ is the dimensions array ($A_i$ has dimensions $p_i \times p_{i+1}$). **Base cases:** $dp[i][i] = 0$ — single matrix, no multiplication needed.

```cpp
#include <iostream>
#include <vector>
#include <climits>
using namespace std;

// Time: O(n^3), Space: O(n^2)
int matrixChain(vector<int>& p) {
    int n = p.size() - 1;  // number of matrices
    vector<vector<int>> dp(n, vector<int>(n, 0));

    // Fill by chain length
    for (int len = 2; len <= n; len++) {
        for (int i = 0; i <= n - len; i++) {
            int j = i + len - 1;
            dp[i][j] = INT_MAX;
            for (int k = i; k < j; k++) {
                int cost = dp[i][k] + dp[k+1][j] + p[i]*p[k+1]*p[j+1];
                dp[i][j] = min(dp[i][j], cost);
            }
        }
    }
    return dp[0][n-1];
}

int main() {
    // Matrices: 10x30, 30x5, 5x60
    vector<int> p = {10, 30, 5, 60};
    cout << matrixChain(p) << endl;  // 4500 (optimal: (A1*A2)*A3)
    return 0;
}
```

**Complexity:** $O(n^3)$ time, $O(n^2)$ space.

---

Now DP on Trees and Grids. Here is `12_04_01_DP_on_Trees.md`:

---

# DP on Trees

Tree DP combines the recursive structure of trees with dynamic programming — we compute answers for subtrees bottom-up, using children's results to build the parent's result.

> **Pattern.** In tree DP, $dp[v]$ stores some quantity about the subtree rooted at $v$. It is computed from $dp[\text{children of } v]$ in a single DFS pass.

---

## Example 1 — Maximum Independent Set on Tree

Find the maximum number of nodes you can select such that no two selected nodes are adjacent (connected by an edge).

> **Subproblem:** $dp[v][0]$ = max nodes in subtree of $v$ when $v$ is NOT selected. $dp[v][1]$ = max nodes when $v$ IS selected. **Recurrence:** $$dp[v][0] = \sum_{\text{child } c} \max(dp[c][0], dp[c][1])$$ $$dp[v][1] = 1 + \sum_{\text{child } c} dp[c][0]$$

```cpp
#include <iostream>
#include <vector>
using namespace std;

vector<vector<int>> adj;
vector<array<int,2>> dp;  // dp[v][0/1]

void dfs(int v, int parent) {
    dp[v][0] = 0;
    dp[v][1] = 1;
    for (int c : adj[v]) {
        if (c == parent) continue;
        dfs(c, v);
        dp[v][0] += max(dp[c][0], dp[c][1]);
        dp[v][1] += dp[c][0];
    }
}

int maxIndependentSet(int n) {
    adj.resize(n); dp.resize(n);
    // ... add edges ...
    dfs(0, -1);
    return max(dp[0][0], dp[0][1]);
}
```

---

## Example 2 — Diameter of Tree (DP formulation)

Already seen in [[07_01_04_Height_and_Diameter]] — here we frame it as tree DP.

> **Subproblem:** $dp[v]$ = height of subtree rooted at $v$. At each node, the diameter passing through $v$ = height of left subtree + height of right subtree + 2.

```cpp
int diameter = 0;

int height(int v, int parent, vector<vector<int>>& adj) {
    int maxH1 = 0, maxH2 = 0;  // two tallest children
    for (int c : adj[v]) {
        if (c == parent) continue;
        int h = height(c, v, adj) + 1;
        if (h > maxH1) { maxH2 = maxH1; maxH1 = h; }
        else if (h > maxH2) maxH2 = h;
    }
    diameter = max(diameter, maxH1 + maxH2);
    return maxH1;
}
```

---

## Example 3 — Rerooting Technique

Sometimes you need $dp[v]$ for every root — not just the chosen root. Rather than running DFS $n$ times, use two DFS passes:

1. First DFS computes subtree answers (downward)
2. Second DFS computes full-tree answers using parent's contribution (upward)

This gives $O(n)$ for what would otherwise be $O(n^2)$.

**Complexity:** All tree DP is $O(n)$ — one or two DFS passes.

---

Here is `12_04_02_DP_on_Grids.md`:

---

# DP on Grids

Grid problems are naturally 2D DP — the grid structure directly maps to a 2D table where each cell depends on neighboring cells.

---

## Unique Paths

Count the number of paths from top-left to bottom-right of an $m \times n$ grid, moving only right or down.

> **Subproblem:** $dp[i][j]$ = number of paths to reach cell $(i,j)$. **Recurrence:** $dp[i][j] = dp[i-1][j] + dp[i][j-1]$ **Base cases:** $dp[0][j] = 1$, $dp[i][0] = 1$ — only one way to reach any cell in the first row or column.

```cpp
#include <iostream>
#include <vector>
using namespace std;

// Time: O(mn), Space: O(mn) — reducible to O(n)
int uniquePaths(int m, int n) {
    vector<vector<int>> dp(m, vector<int>(n, 1));
    for (int i = 1; i < m; i++)
        for (int j = 1; j < n; j++)
            dp[i][j] = dp[i-1][j] + dp[i][j-1];
    return dp[m-1][n-1];
}
```

---

## Minimum Path Sum

Find the path from top-left to bottom-right with minimum total weight, moving only right or down.

```cpp
// Time: O(mn), Space: O(1) in-place
int minPathSum(vector<vector<int>>& grid) {
    int m = grid.size(), n = grid[0].size();

    for (int i = 0; i < m; i++)
        for (int j = 0; j < n; j++) {
            if (i == 0 && j == 0) continue;
            else if (i == 0) grid[i][j] += grid[i][j-1];
            else if (j == 0) grid[i][j] += grid[i-1][j];
            else grid[i][j] += min(grid[i-1][j], grid[i][j-1]);
        }
    return grid[m-1][n-1];
}
```

---

## Largest Rectangle in Binary Matrix

Given a 0-1 matrix, find the largest rectangle containing only 1s. This reduces to the Largest Rectangle in Histogram problem from [[03_04_06_Largest_Rectangle_in_Histogram]] applied row by row.

```cpp
// Uses histogram approach from stack chapter
int maxRectangle(vector<vector<char>>& matrix) {
    if (matrix.empty()) return 0;
    int m = matrix.size(), n = matrix[0].size();
    vector<int> heights(n, 0);
    int maxArea = 0;

    for (int i = 0; i < m; i++) {
        // Update heights — reset to 0 if current cell is '0'
        for (int j = 0; j < n; j++)
            heights[j] = matrix[i][j] == '1' ? heights[j] + 1 : 0;

        // Largest rectangle in current histogram
        stack<int> st;
        for (int j = 0; j <= n; j++) {
            int h = j == n ? 0 : heights[j];
            while (!st.empty() && heights[st.top()] > h) {
                int height = heights[st.top()]; st.pop();
                int width = st.empty() ? j : j - st.top() - 1;
                maxArea = max(maxArea, height * width);
            }
            st.push(j);
        }
    }
    return maxArea;
}

int main() {
    cout << uniquePaths(3, 7) << endl;  // 28

    vector<vector<int>> grid = {{1,3,1},{1,5,1},{4,2,1}};
    cout << minPathSum(grid) << endl;   // 7
    return 0;
}
```

**Complexity:** Unique paths $O(mn)$, min path sum $O(mn)$, largest rectangle $O(mn)$.

---

Now the quiz. Here is `12_QQ_DP_Quiz.md`:

---

## Quiz — Dynamic Programming

**Q1 — Identify DP Applicability** Which of the following have optimal substructure and overlapping subproblems?

- [ ] Fibonacci
- [ ] Binary Search
- [ ] Shortest path in DAG
- [ ] Sorting an array

> [!answer]- Answer Fibonacci and Shortest path in DAG have both properties — DP applies. Binary search has optimal substructure (recurse on the right half) but no overlapping subproblems — each subproblem is unique. Divide and conquer applies. Sorting has neither property in general — no overlapping subproblems. Divide and conquer applies (merge sort, quicksort).

**Q2 — Memoization vs Tabulation** For LCS of two strings of length m and n, compare the memory usage of top-down memoization vs bottom-up tabulation. Which is more space efficient for sparse cases?

> [!answer]- Answer Both use O(mn) in the worst case. However top-down memoization only computes subproblems actually needed — if many (i,j) pairs are never queried, the hash map stores fewer entries. Tabulation always fills the entire mn table. For sparse LCS (strings with few common characters, where only a diagonal band of the table matters), memoization can be significantly more space efficient. For dense cases they are equivalent. In practice tabulation is usually preferred for cache performance.

**Q3 — 0-1 Knapsack** Items: weights=[1,2,3], values=[6,10,12], W=5. Fill the DP table and find the maximum value.

> [!answer]- Answer dp table (rows=items, cols=capacity 0..5): Init: all 0s. Item 1 (w=1,v=6): dp[1][j] = max(dp[0][j], dp[0][j-1]+6) for j>=1. dp[1] = [0, 6, 6, 6, 6, 6]. Item 2 (w=2,v=10): dp[2][j] = max(dp[1][j], dp[1][j-2]+10) for j>=2. dp[2] = [0, 6, 10, 16, 16, 16]. Item 3 (w=3,v=12): dp[3][j] = max(dp[2][j], dp[2][j-3]+12) for j>=3. dp[3] = [0, 6, 10, 16, 18, 22]. Answer: 22 (items 2+3, values 10+12, weights 2+3=5).

**Q4 — Edit Distance Trace** Compute edit distance between "cat" and "cut". Fill the DP table.

> [!answer]- Answer X="cat", Y="cut". dp[i][j] = edit distance between X[0..i-1] and Y[0..j-1]. Base: dp[0][j]=j, dp[i][0]=i. "" c u t "" 0 1 2 3 c 1 0 1 2 a 2 1 1 2 t 3 2 2 1 Answer: dp[3][3] = 1 (replace 'a' with 'u').

**Q5 — LIS** Find LIS of [3, 10, 2, 1, 20]. Show dp array values.

> [!answer]- Answer dp[i] = length of LIS ending at index i. dp[0]=1 (just 3). dp[1]=2 (3,10). dp[2]=2 (2 alone is 1, but 3>2... wait. j<2: arr[0]=3>2, arr[1]=10>2. So dp[2]=1). dp[3]=1 (1 alone, all previous > 1). dp[4]=3 (best: 3,10,20 → dp[1]+1=3, or 3,20 → dp[0]+1=2, or 2,20 → dp[2]+1=2, or 1,20 → dp[3]+1=2). Max=3. dp = [1, 2, 1, 1, 3]. LIS length = 3. One LIS: [3, 10, 20].

**Q6 — Coin Change** Coins=[1,3,4], amount=6. What is the minimum coins? Trace dp array.

> [!answer]- Answer dp[0]=0. dp[1]=1 (1). dp[2]=2 (1+1). dp[3]=1 (3). dp[4]=1 (4). dp[5]=2 (1+4 or 3+... 1+4=2 coins). dp[6]=2 (3+3). dp = [0,1,2,1,1,2,2]. Answer: 2 coins (3+3).

**Q7 — Write it Yourself** Given a string, find the length of the longest palindromic subsequence.

> [!answer]- Answer This is LCS of the string with its reverse.
> 
> ```cpp
> int longestPalindromicSubseq(string s) {
>     string t = string(s.rbegin(), s.rend());
>     int n = s.size();
>     vector<vector<int>> dp(n+1, vector<int>(n+1, 0));
>     for (int i = 1; i <= n; i++)
>         for (int j = 1; j <= n; j++)
>             if (s[i-1] == t[j-1]) dp[i][j] = dp[i-1][j-1] + 1;
>             else dp[i][j] = max(dp[i-1][j], dp[i][j-1]);
>     return dp[n][n];
> }
> ```
> 
> For "bbbab": reverse = "babbb". LCS = "bbbb" length 4. Time O(n^2), Space O(n^2).

**Q8 — Grid DP** In a 3×3 grid with values [[1,3,1],[1,5,1],[4,2,1]], find the minimum path sum from top-left to bottom-right (only right/down moves).

> [!answer]- Answer Fill grid in-place: Row 0: [1, 4, 5]. Row 1: [2, 7, 6]. Row 2: [6, 8, 7]. Answer: 7. Path: 1→1→5→1→1 wait — 1+1+5+1+1=9? Let me recheck. 1→3→1→1→1=7 (right,right,down,down) or 1→1→1→1→... right path: dp[0]=[1,4,5]. dp[1][0]=1+1=2, dp[1][1]=min(4,2)+5=7, dp[1][2]=min(5,7)+1=6. dp[2][0]=2+4=6, dp[2][1]=min(7,6)+2=8, dp[2][2]=min(6,8)+1=7. Answer: 7.

---

And `12_00_Dynamic_Programming.md`:

---

# Dynamic Programming

## Notes

- [[12_01_Memoization_vs_Tabulation]]

## Subfolders

- [[12_02_00_1D_DP]]
- [[12_03_00_2D_DP]]
- [[12_04_00_DP_on_Trees_and_Grids]]

## Quiz

- [[12_QQ_DP_Quiz]]

---

## Quick Reference

### When to Use DP

Both conditions must hold:

- **Optimal substructure** — optimal solution built from optimal subproblems
- **Overlapping subproblems** — same subproblems solved repeatedly

### Design Steps

1. Define $dp[i]$ (what it represents)
2. Write recurrence
3. Identify base cases
4. Determine evaluation order

### Key Problems and Recurrences

|Problem|Recurrence|Time|Space|
|---|---|---|---|
|Fibonacci|$dp[n] = dp[n-1] + dp[n-2]$|$O(n)$|$O(1)$|
|Climbing Stairs|$dp[i] = dp[i-1] + dp[i-2]$|$O(n)$|$O(1)$|
|House Robber|$dp[i] = \max(dp[i-1], dp[i-2]+nums[i])$|$O(n)$|$O(1)$|
|Coin Change|$dp[i] = \min_c(dp[i-c]+1)$|$O(nW)$|$O(W)$|
|LCS|$dp[i][j] = dp[i-1][j-1]+1$ or $\max(dp[i-1][j], dp[i][j-1])$|$O(mn)$|$O(mn)$|
|LIS|$dp[i] = 1 + \max_{j<i, arr[j]<arr[i]} dp[j]$|$O(n^2)$ / $O(n\log n)$|$O(n)$|
|0-1 Knapsack|$dp[i][j] = \max(dp[i-1][j], dp[i-1][j-w]+v)$|$O(nW)$|$O(W)$|
|Edit Distance|$dp[i][j] = dp[i-1][j-1]$ or $1+\min(\ldots)$|$O(mn)$|$O(mn)$|

### Memoization vs Tabulation

||Top Down|Bottom Up|
|---|---|---|
|Stack|$O(n)$ recursion|$O(1)$|
|Computes|Only needed|All subproblems|
|Cache|Hash map or array|Array|

---

Subfolder indexes:

`12_02_00_1D_DP.md`:

# 1D DP

## Notes

- [[12_02_01_Fibonacci]]
- [[12_02_02_Climbing_Stairs]]
- [[12_02_03_House_Robber]]
- [[12_02_04_Coin_Change]]

`12_03_00_2D_DP.md`:

# 2D DP

## Notes

- [[12_03_01_LCS_Longest_Common_Subsequence]]
- [[12_03_02_LIS_Longest_Increasing_Subsequence]]
- [[12_03_03_0_1_Knapsack]]
- [[12_03_04_Edit_Distance]]
- [[12_03_05_Matrix_Chain_Multiplication]]

`12_04_00_DP_on_Trees_and_Grids.md`:

# DP on Trees and Grids

## Notes

- [[12_04_01_DP_on_Trees]]
- [[12_04_02_DP_on_Grids]]

---

Now Backtracking. Here is `13_01_Backtracking_Introduction.md`:

---

# Backtracking Introduction

Dynamic programming explores all subproblems but stores results to avoid recomputation. **Backtracking** explores all possibilities but prunes branches that cannot lead to a valid solution — abandoning a partial solution as soon as it is determined to be invalid.

> **Definition.** _Backtracking_ is a general algorithmic technique that builds a solution incrementally, abandoning a partial solution ("backtracking") as soon as it determines that the partial solution cannot be extended to a valid complete solution.

Think of navigating a maze. You walk down a corridor until you hit a dead end, then retrace your steps to the last junction and try a different corridor. Backtracking does this systematically for all junctions.

The search space is a **state space tree** — each node is a partial solution, each edge is a choice, and leaves are complete solutions (valid or invalid). Backtracking prunes entire subtrees rather than exploring every leaf.

---

## The Template

Every backtracking algorithm follows this structure:

```cpp
void backtrack(state, choices) {
    if (isSolution(state)) {
        recordSolution(state);
        return;
    }
    for (choice : validChoices(state)) {
        makeChoice(state, choice);
        backtrack(state, remaining choices);
        undoChoice(state, choice);  // backtrack
    }
}
```

The **make → recurse → undo** pattern is the core of backtracking. The undo step is what allows the same state variable to be reused across different branches.

---

## When Backtracking Applies

- **Constraint satisfaction problems** — place N queens, solve Sudoku
- **Combinatorial enumeration** — generate all subsets, permutations, combinations
- **Optimization** — find the best solution among all valid ones

The time complexity is typically exponential — $O(k^n)$ or $O(n!)$ — because backtracking explores an exponential search space. Pruning reduces the constant factor but rarely improves the worst-case asymptotic complexity.

> 📖 For backtracking patterns: [LeetCode Backtracking Pattern](https://leetcode.com/tag/backtracking/)

---

Here is `13_02_N_Queens.md`:

---

# N Queens

Place $n$ queens on an $n \times n$ chessboard such that no two queens threaten each other (no two queens share a row, column, or diagonal).

> **State:** current row being filled, positions of queens placed so far. **Choice:** which column to place the queen in the current row. **Constraint:** no two queens share a column or diagonal. **Pruning:** skip any column that conflicts with already-placed queens.

```cpp
#include <iostream>
#include <vector>
#include <string>
using namespace std;

class NQueens {
    int n;
    vector<vector<string>> solutions;
    vector<int> queens;  // queens[row] = column of queen in that row

    bool isValid(int row, int col) {
        for (int r = 0; r < row; r++) {
            if (queens[r] == col) return false;  // same column
            if (abs(queens[r] - col) == abs(r - row)) return false;  // diagonal
        }
        return true;
    }

    void backtrack(int row) {
        if (row == n) {
            // Record solution
            vector<string> board(n, string(n, '.'));
            for (int r = 0; r < n; r++) board[r][queens[r]] = 'Q';
            solutions.push_back(board);
            return;
        }
        for (int col = 0; col < n; col++) {
            if (isValid(row, col)) {
                queens[row] = col;       // make choice
                backtrack(row + 1);
                queens[row] = -1;        // undo choice
            }
        }
    }

public:
    vector<vector<string>> solve(int n) {
        this->n = n;
        queens.assign(n, -1);
        backtrack(0);
        return solutions;
    }
};

int main() {
    NQueens nq;
    auto sols = nq.solve(4);
    cout << sols.size() << " solutions" << endl;  // 2
    for (auto& board : sols) {
        for (auto& row : board) cout << row << endl;
        cout << endl;
    }
    return 0;
}
```

---

## Optimized with Bitsets

Track which columns and diagonals are under attack using bitmasks — $O(1)$ validity check instead of $O(n)$.

```cpp
int totalNQueens(int n) {
    int count = 0;
    function<void(int,int,int,int)> bt = [&](int row, int cols, int diag1, int diag2) {
        if (row == n) { count++; return; }
        int available = ((1 << n) - 1) & ~(cols | diag1 | diag2);
        while (available) {
            int bit = available & (-available);  // lowest set bit
            available &= available - 1;
            bt(row+1, cols|bit, (diag1|bit)<<1, (diag2|bit)>>1);
        }
    };
    bt(0, 0, 0, 0);
    return count;
}
```

**Complexity:** $O(n!)$ worst case — exponential but significantly pruned in practice.

---

Here is `13_03_Sudoku_Solver.md`:

---

# Sudoku Solver

Fill a $9 \times 9$ Sudoku grid such that every row, column, and $3 \times 3$ box contains digits 1-9 exactly once.

> **State:** current partially filled board. **Choice:** digit to place in the next empty cell. **Constraint:** digit must not appear in the same row, column, or box. **Pruning:** if no valid digit exists for a cell, backtrack immediately.

```cpp
#include <iostream>
#include <vector>
using namespace std;

class SudokuSolver {
    bool isValid(vector<vector<char>>& board, int row, int col, char c) {
        for (int i = 0; i < 9; i++) {
            if (board[row][i] == c) return false;  // same row
            if (board[i][col] == c) return false;  // same column
            // Same 3x3 box
            int boxRow = 3*(row/3) + i/3;
            int boxCol = 3*(col/3) + i%3;
            if (board[boxRow][boxCol] == c) return false;
        }
        return true;
    }

    bool backtrack(vector<vector<char>>& board) {
        for (int r = 0; r < 9; r++) {
            for (int c = 0; c < 9; c++) {
                if (board[r][c] != '.') continue;  // skip filled cells

                for (char digit = '1'; digit <= '9'; digit++) {
                    if (isValid(board, r, c, digit)) {
                        board[r][c] = digit;       // make choice
                        if (backtrack(board)) return true;
                        board[r][c] = '.';          // undo choice
                    }
                }
                return false;  // no valid digit — backtrack
            }
        }
        return true;  // all cells filled
    }

public:
    void solve(vector<vector<char>>& board) { backtrack(board); }
};
```

---

## Optimization — Constraint Propagation

Before placing a digit, propagate constraints: eliminate that digit from all cells in the same row, column, and box. This is how human Sudoku solvers work — and it dramatically reduces the search space.

Using bitsets to track available digits per row/column/box:

```cpp
// Track used digits as bitmasks
int rowUsed[9] = {}, colUsed[9] = {}, boxUsed[9] = {};

bool isAvailable(int r, int c, int d) {
    int bit = 1 << d;
    int box = (r/3)*3 + c/3;
    return !(rowUsed[r] & bit) && !(colUsed[c] & bit) && !(boxUsed[box] & bit);
}
```

**Complexity:** $O(9^{81})$ worst case but in practice extremely fast with pruning — valid Sudoku puzzles are solved in milliseconds.

---

Here is `13_04_Subsets_and_Permutations.md`:

---

# Subsets and Permutations

Two of the most fundamental backtracking problems — generating all subsets and all permutations of a collection.

---

## All Subsets

Every element is either included or excluded — $2^n$ subsets total.

```cpp
#include <iostream>
#include <vector>
using namespace std;

// Time: O(2^n * n), Space: O(n) recursion depth
void subsets(vector<int>& nums, int start, vector<int>& curr,
             vector<vector<int>>& result) {
    result.push_back(curr);  // every state is a valid subset
    for (int i = start; i < nums.size(); i++) {
        curr.push_back(nums[i]);      // include nums[i]
        subsets(nums, i+1, curr, result);
        curr.pop_back();              // exclude nums[i]
    }
}

// Subsets with duplicates — sort first, skip duplicate choices
void subsetsWithDup(vector<int>& nums, int start, vector<int>& curr,
                    vector<vector<int>>& result) {
    result.push_back(curr);
    for (int i = start; i < nums.size(); i++) {
        if (i > start && nums[i] == nums[i-1]) continue;  // skip duplicate
        curr.push_back(nums[i]);
        subsetsWithDup(nums, i+1, curr, result);
        curr.pop_back();
    }
}
```

---

## All Permutations

Every arrangement of all elements — $n!$ permutations total.

```cpp
// Time: O(n! * n), Space: O(n)
void permutations(vector<int>& nums, int start, vector<vector<int>>& result) {
    if (start == nums.size()) { result.push_back(nums); return; }
    for (int i = start; i < nums.size(); i++) {
        swap(nums[start], nums[i]);          // make choice
        permutations(nums, start+1, result);
        swap(nums[start], nums[i]);          // undo choice
    }
}

// Permutations with duplicates — use visited array
void permsWithDup(vector<int>& nums, vector<bool>& used,
                  vector<int>& curr, vector<vector<int>>& result) {
    if (curr.size() == nums.size()) { result.push_back(curr); return; }
    for (int i = 0; i < nums.size(); i++) {
        if (used[i]) continue;
        if (i > 0 && nums[i] == nums[i-1] && !used[i-1]) continue;  // skip dup
        used[i] = true;
        curr.push_back(nums[i]);
        permsWithDup(nums, used, curr, result);
        curr.pop_back();
        used[i] = false;
    }
}

int main() {
    vector<int> nums = {1, 2, 3};
    vector<vector<int>> result;
    vector<int> curr;

    subsets(nums, 0, curr, result);
    cout << result.size() << " subsets" << endl;  // 8

    result.clear();
    permutations(nums, 0, result);
    cout << result.size() << " permutations" << endl;  // 6
    return 0;
}
```

---

## Combinations

Choose exactly $k$ elements from $n$ — $\binom{n}{k}$ results.

```cpp
void combinations(int n, int k, int start,
                  vector<int>& curr, vector<vector<int>>& result) {
    if (curr.size() == k) { result.push_back(curr); return; }
    // Pruning: enough elements remaining?
    for (int i = start; i <= n - (k - curr.size()) + 1; i++) {
        curr.push_back(i);
        combinations(n, k, i+1, curr, result);
        curr.pop_back();
    }
}
```

The pruning condition `i <= n - (k - curr.size()) + 1` stops when there are not enough remaining elements to fill the combination — a key optimization.

---

Here is `13_QQ_Backtracking_Quiz.md`:

---

## Quiz — Backtracking

**Q1 — Count Subsets** How many subsets does [1,2,3,4] have? How many have sum exactly 5?

> [!answer]- Answer Total subsets = 2^4 = 16. Subsets with sum 5: {1,4}, {2,3}, {5}... wait, 5 is not in the array. {1,4}=5, {2,3}=5. That's 2 subsets. (Also check: {1,2,3,4}=10, {1,2,4}=7, {1,3,4}=8, {2,3,4}=9, {1,2,3}=6, {1,4}=5✓, {2,3}=5✓, {1,2}=3, {1,3}=4, {2,4}=6, {3,4}=7, singletons and empty don't sum to 5). Answer: 2.

**Q2 — N Queens** For n=4, how many valid placements exist? Give one solution.

> [!answer]- Answer 2 solutions for n=4. One solution: queens at columns [1,3,0,2] (0-indexed). Board: .Q.. ...Q Q... ..Q. Verify: no two queens share a row (one per row by construction), columns 1,3,0,2 all distinct, diagonals: |1-3|=2≠|0-1|=1 ✓, |1-0|=1≠|0-2|=2 ✓, etc. Valid.

**Q3 — Trace Backtracking** Trace the backtracking for generating all subsets of [1,2] — show the call tree.

> [!answer]- Answer Call tree: backtrack(start=0, curr=[]) ├─ include 1: backtrack(start=1, curr=[1]) │ ├─ include 2: backtrack(start=2, curr=[1,2]) → record [1,2], return │ └─ (loop ends) → record [1] on way back └─ include 2: backtrack(start=2, curr=[2]) → record [2], return Records (in order): [], [1,2], [1], [2]. Total 4 = 2^2 subsets. ([] is recorded at very start before any includes.)

**Q4 — Pruning** In the combinations(n=10, k=4) problem, at what point does the pruning condition `i <= n - (k - curr.size()) + 1` kick in? Give a concrete example.

> [!answer]- Answer When curr.size()=3 (3 elements chosen, need 1 more), the loop runs i <= 10 - (4-3) + 1 = 10. Normal. When curr.size()=0 (0 chosen, need 4 more), loop runs i <= 10 - 4 + 1 = 7. So we never start a combination with first element > 7, because we'd need 3 more elements from {8,9,10} = only 3 elements but need elements > first element. Without pruning: try i=8, then need 3 elements from {9,10} = impossible but we'd only discover this after going deeper. With pruning: skip i=8,9,10 immediately. Saves exploring entire subtrees.

**Q5 — Write it Yourself** Generate all valid combinations of parentheses for n pairs.

> [!answer]- Answer
> 
> ```cpp
> void generateParens(int n, int open, int close,
>                     string curr, vector<string>& result) {
>     if (curr.size() == 2*n) { result.push_back(curr); return; }
>     if (open < n)
>         generateParens(n, open+1, close, curr+"(", result);
>     if (close < open)
>         generateParens(n, open, close+1, curr+")", result);
> }
> ```
> 
> For n=3: ["((()))","(()())","(())()","()(())","()()()"] — 5 solutions (Catalan number C_3 = 5). Pruning: only add '(' if open < n, only add ')' if close < open (can't close more than opened). These two conditions together ensure validity and avoid all invalid states.

---

And `13_00_Backtracking.md`:

---

# Backtracking

## Notes

- [[13_01_Backtracking_Introduction]]
- [[13_02_N_Queens]]
- [[13_03_Sudoku_Solver]]
- [[13_04_Subsets_and_Permutations]]

## Quiz

- [[13_QQ_Backtracking_Quiz]]

---

## Quick Reference

### Template

```
backtrack(state):
    if isSolution(state): record and return
    for choice in validChoices(state):
        makeChoice(state, choice)
        backtrack(state)
        undoChoice(state, choice)
```

### Complexities

|Problem|Time|Space|
|---|---|---|
|All subsets|$O(2^n \cdot n)$|$O(n)$|
|All permutations|$O(n! \cdot n)$|$O(n)$|
|Combinations $\binom{n}{k}$|$O(\binom{n}{k} \cdot k)$|$O(k)$|
|N Queens|$O(n!)$ pruned|$O(n)$|
|Sudoku|$O(9^{81})$ pruned|$O(81)$|

### Key Pruning Conditions

|Problem|Pruning condition|
|---|---|
|Combinations|Stop when not enough elements remain|
|N Queens|Skip column if row/column/diagonal conflict|
|Subsets with dups|Skip if same value at same depth|
|Parentheses|Only add ')' if close < open|

---

Now Greedy. Here is `14_01_Greedy_Introduction.md`:

---

# Greedy Introduction

Dynamic programming considers all possible choices and picks the best. **Greedy algorithms** make the locally optimal choice at each step without reconsidering — trusting that local optimality leads to global optimality.

> **Definition.** A _greedy algorithm_ makes the choice that looks best at the current moment — the locally optimal choice — and never backtracks. It is correct when the problem has the **greedy choice property** and **optimal substructure**.

> **Definition.** The _greedy choice property_ means a globally optimal solution can be reached by making locally optimal (greedy) choices. The choice made at each step need not depend on choices yet to be made.

The key question for any greedy algorithm — **can we prove that the greedy choice is safe?** The two standard proof techniques are:

**Exchange argument** — assume an optimal solution that differs from the greedy solution. Show you can exchange its first differing choice for the greedy choice without making the solution worse. Repeat until the optimal solution equals the greedy solution.

**Inductive proof** — show the greedy choice leaves a subproblem that is at least as good as any other choice.

---

## Greedy vs DP

||Greedy|DP|
|---|---|---|
|Choices|One at a time, never reconsidered|All subproblems considered|
|Correctness|Requires proof of greedy choice property|Always correct if recurrence is right|
|Time|Usually $O(n \log n)$ or better|Usually $O(n^2)$ or more|
|When|Greedy choice property holds|Overlapping subproblems, no greedy choice property|

Classic example — **0-1 Knapsack** has no greedy choice property (must use DP), but **fractional knapsack** does (greedy by value/weight ratio works).

---

Here is `14_02_Activity_Selection.md`:

---

# Activity Selection

Given $n$ activities each with a start time $s_i$ and finish time $f_i$, select the maximum number of non-overlapping activities.

> **Greedy choice:** always select the activity with the earliest finish time that doesn't conflict with already-selected activities.

**Proof (exchange argument):** Suppose an optimal solution does not include the activity $a_1$ with the earliest finish time. Since $a_1$ finishes before any other activity, we can replace the first selected activity in the optimal solution with $a_1$ — $a_1$ finishes no later, so it conflicts with no more future activities. The solution remains optimal. Hence we can always include $a_1$.

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

struct Activity { int start, finish, idx; };

// Time: O(n log n) for sort, O(n) selection
vector<int> activitySelection(vector<Activity>& acts) {
    sort(acts.begin(), acts.end(),
         [](const Activity& a, const Activity& b) {
             return a.finish < b.finish;  // sort by finish time
         });

    vector<int> selected;
    int lastFinish = -1;

    for (auto& a : acts) {
        if (a.start >= lastFinish) {  // no conflict
            selected.push_back(a.idx);
            lastFinish = a.finish;
        }
    }
    return selected;
}

int main() {
    vector<Activity> acts = {
        {1,2,0}, {3,4,1}, {0,6,2}, {5,7,3},
        {8,9,4}, {5,9,5}, {3,8,6}
    };
    auto sel = activitySelection(acts);
    cout << sel.size() << " activities selected: ";
    for (int i : sel) cout << i << " ";  // 3 activities
    return 0;
}
```

**Complexity:** $O(n \log n)$ dominated by sort.

---

Here is `14_03_Fractional_Knapsack.md`:

---

# Fractional Knapsack

Unlike 0-1 Knapsack where items are indivisible, in the fractional variant you can take any fraction of an item.

> **Greedy choice:** sort items by value-to-weight ratio in descending order. Take as much of each item as possible (or the entire item if it fits).

**Proof:** Suppose we take a fraction of item $j$ instead of item $i$ where $v_i/w_i > v_j/w_j$. Replacing an equal weight of $j$ with $i$ increases total value. So the greedy choice (highest ratio first) is optimal.

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

struct Item { int weight, value; double ratio; };

// Time: O(n log n), Space: O(1)
double fractionalKnapsack(vector<Item>& items, int W) {
    sort(items.begin(), items.end(),
         [](const Item& a, const Item& b) {
             return a.ratio > b.ratio;  // sort by value/weight descending
         });

    double totalValue = 0;
    int remaining = W;

    for (auto& item : items) {
        if (remaining <= 0) break;
        if (item.weight <= remaining) {
            totalValue += item.value;    // take full item
            remaining  -= item.weight;
        } else {
            totalValue += (double)remaining / item.weight * item.value;
            remaining = 0;              // take fraction
        }
    }
    return totalValue;
}

int main() {
    vector<Item> items = {
        {10, 60, 6.0}, {20, 100, 5.0}, {30, 120, 4.0}
    };
    cout << fractionalKnapsack(items, 50) << endl;  // 240.0
    return 0;
}
```

**Complexity:** $O(n \log n)$ for sort, $O(n)$ selection.

---

Here is `14_04_Huffman_Encoding.md`:

---

# Huffman Encoding

Given character frequencies, assign variable-length binary codes to minimize total encoded length. Frequent characters get shorter codes, rare characters get longer ones.

> **Greedy choice:** always merge the two nodes with the lowest frequencies — they will have the longest codes, so making them as deep as possible minimizes total encoded bits.

> **Definition.** A _Huffman tree_ is a full binary tree where each leaf represents a character. The code for each character is its path from root to leaf (0 = left, 1 = right). Huffman coding produces the optimal prefix-free code.

```cpp
#include <iostream>
#include <queue>
#include <unordered_map>
#include <string>
using namespace std;

struct HuffNode {
    char ch;
    int freq;
    HuffNode* left;
    HuffNode* right;
    HuffNode(char c, int f) : ch(c), freq(f), left(nullptr), right(nullptr) {}
};

struct Compare {
    bool operator()(HuffNode* a, HuffNode* b) { return a->freq > b->freq; }
};

// Build Huffman tree — Time: O(n log n)
HuffNode* buildTree(unordered_map<char,int>& freq) {
    priority_queue<HuffNode*, vector<HuffNode*>, Compare> minPQ;

    for (auto& [ch, f] : freq)
        minPQ.push(new HuffNode(ch, f));

    while (minPQ.size() > 1) {
        HuffNode* left  = minPQ.top(); minPQ.pop();
        HuffNode* right = minPQ.top(); minPQ.pop();
        HuffNode* merged = new HuffNode('\0', left->freq + right->freq);
        merged->left  = left;
        merged->right = right;
        minPQ.push(merged);
    }
    return minPQ.top();
}

// Generate codes by traversing tree
void generateCodes(HuffNode* root, string code,
                   unordered_map<char,string>& codes) {
    if (!root) return;
    if (!root->left && !root->right) {
        codes[root->ch] = code.empty() ? "0" : code;  // single character edge case
        return;
    }
    generateCodes(root->left,  code + "0", codes);
    generateCodes(root->right, code + "1", codes);
}

int main() {
    string text = "abracadabra";
    unordered_map<char,int> freq;
    for (char c : text) freq[c]++;

    HuffNode* root = buildTree(freq);
    unordered_map<char,string> codes;
    generateCodes(root, "", codes);

    cout << "Character codes:" << endl;
    for (auto& [ch, code] : codes)
        cout << ch << ": " << code << " (freq=" << freq[ch] << ")" << endl;

    // Compute encoded length
    int encodedLen = 0;
    for (auto& [ch, code] : codes)
        encodedLen += freq[ch] * code.size();
    cout << "Encoded length: " << encodedLen << " bits" << endl;
    cout << "Original (8-bit ASCII): " << text.size() * 8 << " bits" << endl;
    return 0;
}
```

---

## Optimality Proof Sketch

At the bottom of any optimal prefix-free code tree, there must be two leaves at maximum depth. We can always swap them so the two lowest-frequency characters are at the deepest level without increasing total cost. This justifies always merging the two minimum-frequency nodes.

**Complexity:** $O(n \log n)$ — $n$ insertions and $n-1$ merges each $O(\log n)$ in the min-heap.

---

Here is `14_05_Job_Scheduling.md`:

---

# Job Scheduling

Given $n$ jobs each with a deadline $d_i$ and profit $p_i$ (all taking unit time), schedule jobs to maximize profit. Only one job can run at a time and each job must finish by its deadline.

> **Greedy choice:** sort jobs by profit in descending order. For each job, assign it to the latest available time slot before its deadline.

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

struct Job { int id, deadline, profit; };

// Time: O(n^2), Space: O(n)
vector<int> jobScheduling(vector<Job>& jobs) {
    sort(jobs.begin(), jobs.end(),
         [](const Job& a, const Job& b) { return a.profit > b.profit; });

    int maxDeadline = 0;
    for (auto& j : jobs) maxDeadline = max(maxDeadline, j.deadline);

    vector<int> slot(maxDeadline + 1, -1);  // slot[t] = job assigned to time t
    vector<int> selected;

    for (auto& j : jobs) {
        // Find latest free slot before deadline
        for (int t = j.deadline; t >= 1; t--) {
            if (slot[t] == -1) {
                slot[t] = j.id;
                selected.push_back(j.id);
                break;
            }
        }
    }
    return selected;
}

// O(n log n) version using Union-Find — find the latest free slot efficiently
int findSlot(vector<int>& parent, int t) {
    if (parent[t] == t) return t;
    return parent[t] = findSlot(parent, parent[t]);
}

vector<int> jobSchedulingFast(vector<Job>& jobs, int maxDeadline) {
    sort(jobs.begin(), jobs.end(),
         [](const Job& a, const Job& b) { return a.profit > b.profit; });

    vector<int> parent(maxDeadline + 1);
    iota(parent.begin(), parent.end(), 0);  // parent[i] = i initially
    vector<int> selected;

    for (auto& j : jobs) {
        int slot = findSlot(parent, j.deadline);
        if (slot > 0) {
            selected.push_back(j.id);
            parent[slot] = slot - 1;  // mark slot as used, point to previous
        }
    }
    return selected;
}

int main() {
    vector<Job> jobs = {{1,4,20},{2,1,10},{3,1,40},{4,1,30}};
    auto result = jobScheduling(jobs);
    int profit = 0;
    for (int id : result) {
        for (auto& j : jobs) if (j.id == id) profit += j.profit;
    }
    cout << "Max profit: " << profit << endl;  // 60 (jobs 1 and 3)
    return 0;
}
```

**Complexity:** Naive $O(n^2)$, Union-Find version $O(n \log n)$.

---

Here is `14_QQ_Greedy_Quiz.md`:

---

## Quiz — Greedy Algorithms

**Q1 — Greedy vs DP** For each problem, identify whether greedy or DP is appropriate and why:

- Fractional Knapsack
- 0-1 Knapsack
- Activity Selection
- Longest Common Subsequence

> [!answer]- Answer Fractional Knapsack → Greedy. Can take fractions, so always taking highest value/weight ratio item is provably optimal. 0-1 Knapsack → DP. Items are indivisible. Greedy by ratio can fail: items [(weight=10,val=10),(w=6,val=7),(w=6,val=7)] with W=12. Greedy takes item 1 (ratio 1.0) leaving no room. DP takes items 2+3 for value 14. Activity Selection → Greedy. Earliest finish time greedy is provably optimal by exchange argument. LCS → DP. No greedy choice property — choosing locally "best" matching characters may miss longer subsequences.

**Q2 — Activity Selection by Hand** Activities: [(0,6),(1,4),(3,5),(3,9),(5,7),(5,9),(6,10),(8,11),(8,12),(2,14),(12,16)]. Select maximum non-overlapping activities using greedy.

> [!answer]- Answer Sort by finish time: (1,4),(3,5),(3,9),(0,6),(5,7),(5,9),(6,10),(8,11),(8,12),(2,14),(12,16). Select (1,4): lastFinish=4. Next (3,5): start=3<4 conflict skip? No wait 3<4=lastFinish, conflict. Actually start>=lastFinish is required. 3<4, conflict, skip. Hmm, let me redo. Actually (1,4) means start=1,finish=4. Select it, lastFinish=4. (3,5): start=3 < lastFinish=4, skip. (3,9): skip. (0,6): skip. (5,7): start=5 >= 4, select, lastFinish=7. (5,9): skip. (6,10): skip. (8,11): start=8>=7, select, lastFinish=11. (8,12): skip. (2,14): skip. (12,16): start=12>=11, select, lastFinish=16. Selected: 4 activities. Intervals: (1,4),(5,7),(8,11),(12,16).

**Q3 — Huffman Codes** Characters and frequencies: a=5, b=2, c=1, d=3, e=4. Build the Huffman tree and give each character's code.

> [!answer]- Answer Min-heap initially: c(1),b(2),d(3),e(4),a(5). Merge c(1)+b(2)=cb(3). Heap: cb(3),d(3),e(4),a(5). Merge cb(3)+d(3)=cbd(6). Heap: e(4),a(5),cbd(6). Merge e(4)+a(5)=ea(9). Heap: cbd(6),ea(9). Merge cbd(6)+ea(9)=root(15). Tree: root→left=cbd(6), right=ea(9). cbd→left=cb(3),right=d(3). cb→left=c(1),right=b(2). ea→left=e(4),right=a(5). Codes: c=000, b=001, d=01, e=10, a=11. Total bits = 5_2 + 4_2 + 3_2 + 2_3 + 1*3 = 10+8+6+6+3 = 33 bits.

**Q4 — Fractional Knapsack** Items: (w=10,v=60),(w=20,v=100),(w=30,v=120), W=50. Find optimal solution.

> [!answer]- Answer Ratios: 60/10=6, 100/20=5, 120/30=4. Sort: item1(r=6), item2(r=5), item3(r=4). Take all item1 (w=10): value=60, remaining=40. Take all item2 (w=20): value+=100, remaining=20. Take 20/30 of item3: value += 20/30 * 120 = 80, remaining=0. Total value = 60+100+80 = 240.

**Q5 — Write it Yourself** Given an array of non-negative integers representing coin denominations and a target, greedily make change. Does the greedy approach always work? Give a counterexample if not.

> [!answer]- Answer
> 
> ```cpp
> vector<int> greedyChange(vector<int>& coins, int target) {
>     sort(coins.rbegin(), coins.rend());  // largest first
>     vector<int> result;
>     for (int c : coins) {
>         while (target >= c) { result.push_back(c); target -= c; }
>     }
>     return result;
> }
> ```
> 
> Greedy does NOT always work. Counterexample: coins=[1,3,4], target=6. Greedy: take 4 (remaining 2), take 1, take 1 → 3 coins. Optimal: 3+3=2 coins. Greedy gives suboptimal answer. Greedy works for canonical coin systems like US coins (1,5,10,25) but fails for arbitrary denominations. DP (coin change) is required for the general case.

---

And finally `14_00_Greedy_Algorithms.md`:

---

# Greedy Algorithms

## Notes

- [[14_01_Greedy_Introduction]]
- [[14_02_Activity_Selection]]
- [[14_03_Fractional_Knapsack]]
- [[14_04_Huffman_Encoding]]
- [[14_05_Job_Scheduling]]

## Quiz

- [[14_QQ_Greedy_Quiz]]

---

## Quick Reference

### When Greedy Works

Both must hold:

- **Greedy choice property** — local optimal → global optimal
- **Optimal substructure** — optimal solution built from optimal subproblems

### Proof Techniques

**Exchange argument:** Assume any optimal solution. Show you can swap its first non-greedy choice for the greedy choice without getting worse. Conclude greedy is optimal.

### Key Problems

|Problem|Greedy strategy|Time|
|---|---|---|
|Activity Selection|Earliest finish time first|$O(n \log n)$|
|Fractional Knapsack|Highest value/weight ratio first|$O(n \log n)$|
|Huffman Encoding|Merge two lowest frequency nodes|$O(n \log n)$|
|Job Scheduling|Highest profit first, latest free slot|$O(n^2)$ / $O(n \log n)$|
|Dijkstra's (shortest path)|Nearest unvisited vertex|$O((V+E)\log V)$|
|Prim's (MST)|Minimum edge crossing cut|$O((V+E)\log V)$|
|Kruskal's (MST)|Minimum weight edge not forming cycle|$O(E \log E)$|

### Greedy vs DP

||Greedy|DP|
|---|---|---|
|0-1 Knapsack|✗|✓|
|Fractional Knapsack|✓|Overkill|
|Shortest path (non-negative)|✓ Dijkstra|Also works|
|Shortest path (negative edges)|✗|✓ Bellman-Ford|
|Coin change (arbitrary)|✗|✓|
|Activity selection|✓|Also works|

---

That completes the entire DSA vault baby! Every chapter from Foundations through Greedy is done — notes, quizzes, and summaries all in the same style throughout. 🎉
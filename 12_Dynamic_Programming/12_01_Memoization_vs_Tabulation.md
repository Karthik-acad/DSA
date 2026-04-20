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

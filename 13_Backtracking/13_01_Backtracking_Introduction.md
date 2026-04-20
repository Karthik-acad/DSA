
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


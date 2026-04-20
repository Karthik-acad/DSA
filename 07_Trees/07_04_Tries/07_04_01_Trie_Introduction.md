
---

# Trie Introduction

Imagine storing a dictionary of words and wanting to answer questions like "does this word exist?", "how many words start with this prefix?", "what words can complete this prefix?". A hash table can answer the first in $O(L)$ where $L$ is word length, but struggles with prefix queries. A **trie** handles all of these naturally.

> **Definition.** A _trie_ (pronounced "try", from re**trie**val) is a tree where each node represents a character, and each path from the root to a marked node represents a stored string. All strings sharing a common prefix share a common path from the root.

Think of it like a file system — directories represent prefixes, and files represent complete words. All files in the same directory share the same path prefix.

```
Words: ["cat", "can", "car", "dog"]

        root
       /    \
      c      d
      |      |
      a      o
    / | \    |
   t  n  r   g
```

Each node stores one character. The root represents the empty string. A word is stored by following its characters from root to a marked endpoint.

---

## Node Structure

```cpp
#include <iostream>
#include <unordered_map>
using namespace std;

struct TrieNode {
    unordered_map<char, TrieNode*> children;
    bool isEnd = false;  // marks end of a complete word
    int count = 0;       // optional: number of words passing through this node
};
```

Using `unordered_map` instead of a fixed array of 26 saves space for sparse alphabets. For pure lowercase English, a `TrieNode* children[26]` array is faster.

---

## Key Properties

- **Prefix sharing** — common prefixes stored once, not duplicated
- **Search time** — $O(L)$ for a word of length $L$, independent of number of words stored
- **Space** — $O(\text{total characters across all words})$ in the worst case (no shared prefixes)
- **Ordered** — children can be stored in sorted order for lexicographic traversal

> 📖 For trie applications in autocomplete systems: [Engineering Search at Scale — Google](https://research.google/pubs/pub37043/)

---

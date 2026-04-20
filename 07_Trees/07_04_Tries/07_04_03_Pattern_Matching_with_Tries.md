
Here is `07_04_03_Pattern_Matching_with_Tries.md`:

---

# Pattern Matching with Tries

Tries shine when you need to match multiple patterns against a text simultaneously. Instead of running each pattern separately, you build a trie of all patterns and traverse the text once.

> **Definition.** _Multi-pattern matching_ using a trie checks all patterns simultaneously by traversing the trie with each character of the text, finding all patterns that appear as substrings.

---

## Dictionary Matching

Given a set of pattern words and a text, find all occurrences of any pattern word starting at each position.

```cpp
// Naive: O(n * m * L) where n=text length, m=patterns, L=avg pattern length
// Trie: O(n * L_max + total pattern length)

#include <iostream>
#include <vector>
#include <string>
using namespace std;

// For each position in text, check if any pattern starts there
vector<pair<int,string>> findPatterns(const string& text,
                                       const vector<string>& patterns) {
    Trie trie;
    for (auto& p : patterns) trie.insert(p);

    vector<pair<int,string>> result;
    for (int i = 0; i < text.size(); i++) {
        // Try to match starting at position i
        TrieNode* curr = trie.root;  // need access to root
        for (int j = i; j < text.size(); j++) {
            char c = text[j];
            if (!curr->children.count(c)) break;
            curr = curr->children[c];
            if (curr->isEnd)
                result.push_back({i, text.substr(i, j-i+1)});
        }
    }
    return result;
}
```

For more advanced multi-pattern matching (finding all occurrences efficiently), the **Aho-Corasick algorithm** extends the trie with failure links — similar to KMP's failure function but for multiple patterns. It achieves $O(n + \sum L_i + \text{matches})$ total — linear in the text length regardless of the number of patterns. This is covered in [[11_02_KMP_Algorithm]] context.

---

## Longest Word in Dictionary Formed by Other Words

```cpp
// Find longest word that can be built one char at a time using other words
// Time: O(sum of word lengths), Space: O(sum of word lengths)
string longestWord(vector<string>& words) {
    Trie trie;
    for (auto& w : words) trie.insert(w);

    string result = "";
    // DFS through trie, only going through nodes that are word endings
    function<void(TrieNode*, string)> dfs = [&](TrieNode* node, string curr) {
        if (curr.size() > result.size()) result = curr;
        for (auto& [c, child] : node->children) {
            if (child->isEnd)
                dfs(child, curr + c);
        }
    };
    dfs(trie.root, "");  // need root access
    return result;
}
```

---


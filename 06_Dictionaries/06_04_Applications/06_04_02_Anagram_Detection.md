
---

# Anagram Detection

Two strings are **anagrams** if one is a rearrangement of the other — they contain exactly the same characters with the same frequencies. Hash maps make anagram detection clean and $O(n)$.

> **Definition.** Two strings are _anagrams_ if they have identical character frequency maps.

```cpp
#include <iostream>
#include <unordered_map>
#include <vector>
#include <string>
using namespace std;

// Check if two strings are anagrams — O(n)
bool isAnagram(const string& s, const string& t) {
    if (s.size() != t.size()) return false;
    unordered_map<char, int> freq;
    for (char c : s) freq[c]++;
    for (char c : t) {
        freq[c]--;
        if (freq[c] < 0) return false;
    }
    return true;
}

// Group anagrams together — O(n * k) where k = avg string length
vector<vector<string>> groupAnagrams(vector<string>& words) {
    unordered_map<string, vector<string>> groups;
    for (string& w : words) {
        string key = w;
        sort(key.begin(), key.end());  // sorted string as key
        groups[key].push_back(w);
    }
    vector<vector<string>> result;
    for (auto& [k, v] : groups) result.push_back(v);
    return result;
}

// Find all anagram substrings — sliding window + hash map — O(n)
vector<int> findAnagrams(const string& s, const string& p) {
    vector<int> result;
    if (s.size() < p.size()) return result;

    unordered_map<char, int> pFreq, windowFreq;
    for (char c : p) pFreq[c]++;

    int k = p.size();
    for (int i = 0; i < s.size(); i++) {
        windowFreq[s[i]]++;
        if (i >= k) {
            char out = s[i - k];
            windowFreq[out]--;
            if (windowFreq[out] == 0) windowFreq.erase(out);
        }
        if (i >= k - 1 && windowFreq == pFreq)
            result.push_back(i - k + 1);
    }
    return result;
}

int main() {
    cout << isAnagram("anagram", "nagaram") << endl;  // 1
    cout << isAnagram("rat", "car") << endl;           // 0

    vector<string> words = {"eat","tea","tan","ate","nat","bat"};
    auto groups = groupAnagrams(words);
    for (auto& g : groups) {
        for (auto& w : g) cout << w << " ";
        cout << endl;
    }

    auto indices = findAnagrams("cbaebabacd", "abc");
    for (int i : indices) cout << i << " ";  // 0 6
    return 0;
}
```

---

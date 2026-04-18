
And finally `02_00_Linked_Lists.md`:

---

# Linked Lists

## Notes

- [[02_01_Singly_Linked_List]]
- [[02_02_Doubly_Linked_List]]
- [[02_03_Circular_Linked_List]]
- [[02_04_Floyd_Cycle_Detection]]
- [[02_05_Runner_Technique]]

## Quiz

- [[02_QQ_Linked_Lists_Quiz]]

---

## Quick Reference

### Node Structure

|Type|Pointers per node|Extra memory|
|---|---|---|
|Singly|`next`|1 pointer|
|Doubly|`next`, `prev`|2 pointers|
|Circular|`next` (points to head)|1 pointer|

---

### Complexity Comparison

|Operation|Singly|Doubly|Array|
|---|---|---|---|
|Access by index|$O(n)$|$O(n)$|$O(1)$|
|Insert at head|$O(1)$|$O(1)$|$O(n)$|
|Insert at tail|$O(n)$ / $O(1)$*|$O(1)$*|$O(1)$ amortized|
|Insert at middle|$O(1)$ given node|$O(1)$ given node|$O(n)$|
|Delete given node|$O(n)$ need prev|$O(1)$|$O(n)$|
|Search|$O(n)$|$O(n)$|$O(n)$|

*$O(1)$ if tail pointer is maintained

---

### Floyd's Cycle Detection

Two pointers — slow moves 1 step, fast moves 2 steps:

|Phase|Action|Result|
|---|---|---|
|Phase 1|Move until slow == fast|Detect cycle|
|Phase 2|Reset slow to head, move both 1 step|Find cycle entry|

$$\text{distance(head → entry)} = \text{distance(meeting point → entry)}$$

---

### Runner Technique Patterns

|Problem|Slow|Fast|Gap|
|---|---|---|---|
|Find middle|1 step|2 steps|—|
|k-th from end|1 step|1 step|k ahead|
|Cycle detection|1 step|2 steps|—|

---
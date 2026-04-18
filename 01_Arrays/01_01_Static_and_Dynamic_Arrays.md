
---
# Static and Dynamic Arrays

From our understanding of data structures, we know that we need some way to store collections of data in memory. The simplest and most natural way to do this is to store them _side by side_ — one after another in a contiguous block of memory. This is exactly what an **array** is.

Think of memory like a long row of numbered boxes. An array reserves a specific set of those boxes — say boxes 100 through 109 for an array of 10 integers — and guarantees they are all next to each other. This adjacency is what makes arrays powerful.

> **Definition.** An _array_ is a collection of elements of the same data type stored in contiguous memory locations, accessible via an index.

Because elements are contiguous and same-typed, the address of any element can be computed directly:

$$\text{address}(i) = \text{base address} + i \times \text{size of one element}$$

This means accessing any element by index is always $O(1)$ — no matter how large the array, you jump straight to it.

---
## Static Arrays

A **static array** is one whose size is fixed at the time of declaration. You tell the compiler upfront how many elements you need, it reserves exactly that much memory, and that is it — you cannot grow or shrink it later.

```cpp
#include <iostream>
using namespace std;

int main() {
    // Size must be known at compile time
    int arr[5] = {10, 20, 30, 40, 50};

    // O(1) access — direct index
    cout << arr[2] << endl;  // 30

    // O(n) traversal
    for (int i = 0; i < 5; i++)
        cout << arr[i] << " ";

    return 0;
}
```

The tradeoff is clear — static arrays are extremely fast and memory-efficient since there is zero overhead, but you must know the size in advance. If you declare too large, you waste memory. If too small, you are stuck.

---

## Dynamic Arrays

A **dynamic array** solves the size problem — it can grow as you add elements. In C++ the standard library gives you `vector<T>` which is a dynamic array under the hood.

The way it works internally is clever. It maintains:

- A pointer to the underlying static array
- The current **size** — how many elements are actually stored
- The current **capacity** — how many elements the underlying array can hold

When you push an element and size equals capacity, it **doubles** the capacity — allocates a new array of twice the size, copies everything over, and frees the old one. This doubling strategy is what keeps the amortized cost of insertion  $O(1)$ .

> **Definition.** *Amortized  $O(1)$  means that while a single operation may occasionally be expensive (the copy when doubling), the average cost per operation over a long sequence is still  $O(1)$ .


```cpp
#include <iostream>
#include <vector>
using namespace std;

int main() {
    vector<int> arr;

    // push_back — amortized O(1)
    arr.push_back(10);
    arr.push_back(20);
    arr.push_back(30);

    // O(1) access
    cout << arr[1] << endl;  // 20

    // size vs capacity
    cout << "Size: " << arr.size() << endl;
    cout << "Capacity: " << arr.capacity() << endl;

    // insert at position — O(n) due to shifting
    arr.insert(arr.begin() + 1, 15);

    // erase at position — O(n) due to shifting
    arr.erase(arr.begin() + 2);

    // O(n) traversal
    for (int x : arr)
        cout << x << " ";

    return 0;
}
```

---

## Complexity Summary

|Operation|Static Array|Dynamic Array (vector)|
|---|---|---|
|Access by index|O(1)O(1) O(1)|O(1)O(1) O(1)|
|Search (unsorted)|O(n)O(n) O(n)|O(n)O(n) O(n)|
|Insert at end|—|O(1)O(1) O(1) amortized|
|Insert at middle|—|O(n)O(n) O(n)|
|Delete at end|—|O(1)O(1) O(1)|
|Delete at middle|—|O(n)O(n) O(n)|
|Memory overhead|None|Small (size + capacity)|

The insert and delete at middle are O(n)O(n) O(n) because all elements after the insertion point must be shifted one position — this is the fundamental weakness of arrays compared to linked lists. However arrays win on access time — linked lists are O(n)O(n) O(n) to access by index while arrays are always O(1)O(1) O(1).

>  For how vectors are implemented internally: [CPP Reference — std::vector](https://en.cppreference.com/w/cpp/container/vector) 
>  For a deeper look at amortized analysis: [MIT 6.006 Lecture 2](https://ocw.mit.edu/courses/6-006-introduction-to-algorithms-spring-2020/pages/lecture-notes/)


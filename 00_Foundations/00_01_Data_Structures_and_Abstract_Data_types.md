
--- 
# Data Structures

From our previous course on Introduction to Programming, we have an understanding of what a **Data Type** is — simply, it is used to distinguish different kinds of data we store. Think of it like this: numbers are different from letters. You use them for different things, and you can do certain operations on each. Similarly, `int` (used to store integers) is different from `char` (used to store characters). Each type tells the computer _how much memory to reserve_ and _what operations make sense_ on that data.

Now here's the next natural question — okay, we can store _one_ integer, _one_ character. But what if we want to store a _hundred_ integers? A _thousand_ names? Just having data types isn't enough. We need some way to _organize_ and _store collections_ of data in memory. This is exactly what **Data Structures** solve.

> **Definition.** A _data structure_ is a way of organizing and storing data in memory such that it can be accessed and modified efficiently.

Think of it like this — data types are the _ingredients_, data structures are the _containers_ you put them in. A jar for jam, a bottle for water, a box for cereal — each container is suited for what it holds and how you use it. Similarly, different data structures suit different problems.

---

## Abstract Data Types

Before we actually look at any specific data structure, there is one more idea worth understanding — the **Abstract Data Type (ADT)**.

You already know abstraction from real life. When you use a TV remote, you press "volume up" and the volume goes up. You don't care _how_ the circuit inside processes that signal — you just care about _what it does_. An ADT works exactly the same way.

An ADT defines:

- **What data** is being stored
- **What operations** you can perform on it

It says _nothing_ about _how_ those operations are implemented internally. That's left to whoever builds it.

> **Definition.** An _Abstract Data Type_ is a mathematical model for a data type, defined by its behavior (operations) from the user's perspective, independent of implementation.

For example, a **Stack** ADT says: you can `push` something on top, `pop` something off the top, and `peek` at the top. Whether it's built using an array or a linked list underneath — that's an implementation detail. The ADT doesn't care.

This separation of _what_ from _how_ is fundamental to how we think about data structures throughout this course.

```cpp
// One way to implement an ADT — using a struct
struct Stack {
    int data[100];
    int top = -1;

    void push(int x) { data[++top] = x; }
    int pop()        { return data[top--]; }
    int peek()       { return data[top]; }
};
```

```cpp
// Another way — using a class with access control
class Stack {
    int data[100];
    int top = -1;
public:
    void push(int x) { data[++top] = x; }
    int pop()        { return data[top--]; }
    int peek()       { return data[top]; }
};
```

Both do the same thing. The `class` version hides internals by default (`private`), which is closer to the true spirit of an ADT — the user only sees the operations. There are other ways to implement this too (using linked lists, dynamic arrays, STL etc.) — we'll see those as we go. For now, the **class-based** implementation is what we'll primarily use going forward.

> For a deeper read on ADTs: 
>  [Introduction to ADTs](https://www.geeksforgeeks.org/abstract-data-types/)
>  [C++ User Defined Types](https://en.cppreference.com/w/cpp/language/type)
>  [Stack using Linked Lists]( https://www.geeksforgeeks.org/dsa/implement-a-stack-using-singly-linked-list/)
>  [Stack using array]([https://www.geeksforgeeks.org/abstract-data-types/](https://www.geeksforgeeks.org/dsa/implement-stack-using-array/))
>  [CMU 15-121 ADT Notes](https://www.cs.cmu.edu/~clo/www/CMU/DataStructures/Lessons/)

---

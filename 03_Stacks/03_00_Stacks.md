
---

# Stacks

## Notes

- [[03_01_Stack_Introduction]]
- [[03_02_Stack_Implementation]]
- [[03_03_Monotonic_Stack]]

## Applications

- [[03_04_00_Applications]]

## Quiz

- [[03_QQ_Stacks_Quiz]]

---

## Quick Reference

### Stack ADT

> **Definition.** A stack is a LIFO (Last In First Out) data structure. The last element inserted is the first one removed.

|Operation|Complexity|
|---|---|
|push|$O(1)$|
|pop|$O(1)$|
|peek / top|$O(1)$|
|isEmpty|$O(1)$|

---

### Implementation Comparison

||Static Array|Dynamic Array|Linked List|
|---|---|---|---|
|Push|$O(1)$|$O(1)$ amortized|$O(1)$|
|Pop|$O(1)$|$O(1)$|$O(1)$|
|Size limit|Fixed|Dynamic|Dynamic|
|Cache|Good|Good|Poor|

---

### Monotonic Stack — The Four Variants

|Problem|Stack order|Pop condition|Record when|
|---|---|---|---|
|NGE|Decreasing|$arr[top] < curr$|On pop|
|NSE|Increasing|$arr[top] > curr$|On pop|
|PGE|Decreasing|$arr[top] \leq curr$|Before push|
|PSE|Increasing|$arr[top] \geq curr$|Before push|

$$\text{span}[i] = i - \text{index of PGE}[i]$$

$$\text{Rectangle area at } i = h[i] \times (\text{NSE}[i] - \text{PSE}[i] - 1)$$

$$\text{Water at } i = \min(\text{maxLeft}[i],\ \text{maxRight}[i]) - h[i]$$

---

### Operator Precedence for Infix Conversion

|Operator|Precedence|
|---|---|
|`^`|3 (highest)|
|`*`, `/`|2|
|`+`, `-`|1 (lowest)|

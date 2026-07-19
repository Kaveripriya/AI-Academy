# Tuples

---

## 1. Learning Objectives

By the end of this unit, you will be able to:

✓ Create a tuple by packing values or with `tuple()`, and access its elements by index or slice.  
✓ Explain immutability — what it forbids, and what it actually guarantees.  
✓ Unpack a tuple into variables, including the `a, b = b, a` swap and star-unpacking.  
✓ Use nested tuples, the `count()`/`index()` methods, and tuple comparison for sorting.  
✓ Decide when a tuple is the right choice over a list.

---

## 2. Overview

Last unit's list could do almost anything — grow, shrink, get sorted, get rewritten mid-program. Python's **tuple** is the opposite trade-off: an ordered collection, written with `( )` instead of `[ ]`, that **cannot be changed once created**.

Think of the difference like wet cement versus cured concrete. Wet cement (a list) is moldable — reshape it as many times as you like while building. Cured concrete (a tuple) has set permanently; you can see it, measure it, build on top of it, but not reshape it. Need a different shape? Don't force the old block to bend — pour a new one. That's exactly how you "change" a tuple: you don't edit it, you replace it.

This unit covers creating and reading tuples, unpacking them, nesting them, what immutability actually buys you, and the short list of operations and methods tuples support.

---

## 3. Description

### 3.1 Creating and Reading a Tuple

A tuple is comma-separated values, conventionally wrapped in parentheses — the comma is what makes it a tuple, not the parentheses:

```python
student = "Priya", 21, "Computer Science"   # parentheses are optional here
print(student[0], student[-1])
```

Output:

```
Priya Computer Science
```

Indexing and slicing work exactly like they did for lists — including slicing with a step, e.g. `student[::-1]` reverses it. What's different is trying to change one:

```python
student[1] = 22
```

```
TypeError: 'tuple' object does not support item assignment
```

A single-element tuple needs a trailing comma: `("Priya",)` is a tuple; `("Priya")` is just a string in redundant parentheses. You can also build a tuple from any existing iterable with `tuple(iterable)` — `tuple([1, 2, 3])` gives `(1, 2, 3)`, and `tuple("abc")` gives `('a', 'b', 'c')`. It takes one iterable, not loose values: `tuple(1, 2, 3)` is a `TypeError`.

### 3.2 Unpacking a Tuple

**Unpacking** takes a tuple's values and assigns each to its own variable, in one line:

```python
name, age, branch = student
```

This is also how Python swaps two variables with no temporary one — it builds the tuple `(b, a)` completely *before* assigning anything back:

```python
a, b = 5, 10
a, b = b, a
print(a, b)
```

```
10 5
```

When you only care about the ends, star-unpacking collects the rest into a list: `first, *rest = (1, 2, 3, 4)` gives `first = 1`, `rest = [2, 3, 4]` — at most one `*` is allowed. Unpacking is also why a function that needs to hand back more than one value almost always returns a tuple, which the caller unpacks — you'll use exactly this in the worked example.

### 3.3 Nested Tuples

A tuple can hold other tuples — the natural way to represent one thing made of paired values, like a location's latitude and longitude:

```python
delivery_point = ("Central Library", (12.9716, 77.5946))
print(delivery_point[1][0])
```

```
12.9716
```

`delivery_point[1]` gets the inner tuple; the second `[0]` reaches inside it.

### 3.4 What Immutability Actually Buys You

If two variables point to the *same* list, changing it through one silently changes what the other sees — a real source of bugs:

```python
scores_a = [78, 82, 90]
scores_b = scores_a
scores_b.append(100)
print(scores_a)          # changed too — nobody edited it directly!
```

```
[78, 82, 90, 100]
```

Do the same with a tuple, and there's no version of this bug — there's no operation that changes it in place at all:

```python
record_a = (78, 82, 90)
record_b = record_a      # shares the same tuple safely — neither can mutate it
```

That guarantee — "this data cannot be quietly changed out from under you" — is the real reason to choose a tuple, not just "it's read-only." Reach for one when the values form a fixed record that should stay exactly as created: coordinates, a multi-part return value, a row you'll only ever read.

### 3.5 Operations, Methods, and Comparison

```python
print((1, 2) + (3, 4))     # (1, 2, 3, 4)  — concatenation, builds a new tuple
print(("a",) * 3)          # ('a', 'a', 'a')  — repetition
print(21 in student)       # True  — membership
```

A tuple has exactly two methods, both read-only — fitting immutability perfectly:

```python
marks = (7, 3, 7, 7, 1)
print(marks.count(7))    # 3 — how many times 7 appears
print(marks.index(7))    # 0 — position of the first 7 (raises ValueError if absent)
```

Tuples also compare **lexicographically** — element by element, left to right, like dictionary order:

```python
print((1, 2) < (1, 3))     # True — first elements tie, 2 < 3 decides
print(sorted([("Ada", 95), ("Alan", 72), ("Ada", 20)]))
```

```
[('Ada', 20), ('Ada', 95), ('Alan', 72)]
```

This is why `sorted()` on a list of tuples gives multi-key ordering for free — the first field breaks ties, then the second, and so on.

---

## 4. Real-World Application

A function like `divmod(17, 5)` returning `(3, 2)` is packing-then-unpacking in Python's own built-ins: both values travel together and unpack in one line. A ride-hailing app's driver-location updates work the same way — every ping is a `(latitude, longitude)` pair, because neither number means anything alone. And a payment gateway's transaction receipt (ID, amount, timestamp) is usually a tuple for the same reason immutability matters here: nothing downstream can silently edit it after the fact.

---

## 5. Worked Example

**Goal:** Write a function that returns three values at once, unpack the result, then see why a tuple is the safer choice for that result than a list.

```python
def mark_summary(marks):
    return min(marks), max(marks), sum(marks) / len(marks)   # packed into a tuple

marks = [78, 65, 90, 55, 82]
low, high, avg = mark_summary(marks)
print(low, high, avg)
```

```
55 90 74.0
```

Try to "correct" the average in place, the way you might with a list:

```python
result = mark_summary(marks)
result[2] = 75.0
```

```
TypeError: 'tuple' object does not support item assignment
```

The correct fix builds a new tuple instead of editing the old one:

```python
result = (result[0], result[1], 75.0)
print(result)
```

```
(55, 90, 75.0)
```

*Common mistake: reaching for `result[2] = 75.0` out of habit from lists. If you want to edit one field of a tuple, either the data should have been a list, or you build a fresh tuple with the corrected value — never patch it in place.*

---

## 6. Summary

- **Tuples** are ordered, comma-separated values — conventionally written with `( )`, or built from any iterable with `tuple(iterable)` — created through packing and read through unpacking.
- **Unpacking** assigns each element to its own variable, enables the `a, b = b, a` swap and star-unpacking (`first, *rest = ...`), and is how functions hand back more than one result.
- **Immutability isn't just a restriction** — it's a guarantee that a value can be shared without any risk of it changing unexpectedly.
- Tuples support **`count()`, `index()`**, concatenation, repetition, membership, and **lexicographic comparison** — which makes `sorted()` on tuples do multi-key ordering for free.
- **Nested tuples** represent values that only make sense together, like a coordinate pair.

Up next: sets — a collection built around a guarantee neither the list nor the tuple gives you: every value in it is unique.

---

*© 2026 Revature · AI Native Engineering — Foundations · Unit 3.2 · Version 1.0*

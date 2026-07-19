# Tuples

---

## 1. Learning Objectives

By the end of this unit, you will be able to:

✓ Create a tuple by packing values or with `tuple()`, and access its elements by index or slice.  
✓ Explain immutability — what it forbids, and why a tuple exposes only two methods.  
✓ Unpack a tuple into variables, including the `a, b = b, a` swap and star-unpacking.  
✓ Use nested tuples, the `count()`/`index()` methods, and lexicographic comparison.  
✓ Decide when a tuple is the right choice over a list, and how to "change" one.

---

## 2. Overview

You just spent the last unit on lists — ordered, indexable, and freely editable. A **tuple** is the same idea with one deliberate restriction: once built, it **cannot be changed**. Where a list says "here is a collection I might edit," a tuple says "here is a fixed group of values that belong together and will not move."

A pair of coordinates, an RGB color, or a database row are groups where changing one value in place would usually be a bug, not a feature — a tuple lets you say "these values travel as a unit and stay put." Deciding "list or tuple?" is one of the first choices you make once you're picking a data structure for a real task.

This unit covers creating and reading tuples (including the `tuple()` constructor), immutability and what it actually buys you, unpacking, nesting, and the operations, methods, and comparison rules tuples support.

---

## 3. Description

### 3.1 What a Tuple Is

A tuple is an ordered sequence of values, written with parentheses and comma-separated values — though the parentheses are actually optional; the comma is what makes it a tuple:

```python
point = 3, 5        # packing — no parentheses needed
rgb = (255, 128, 0)
person = ("Ada", 36, True)   # mixed types are fine, exactly like a list
```

A single-element tuple needs a trailing comma: `(42,)` is a tuple; `(42)` is just the number `42` sitting in redundant parentheses — a common, quiet bug. You can also build a tuple from any existing iterable with `tuple()`:

```python
tuple([1, 2, 3])     # (1, 2, 3)   — freeze a list
tuple("abc")         # ('a', 'b', 'c')
```

`tuple()` takes **one** argument — an iterable. `tuple(1, 2, 3)` raises a `TypeError`; when the values are loose, use the comma form `(1, 2, 3)` instead.

### 3.2 Indexing, Slicing, and Immutability

Because a tuple is a sequence, everything you know about reading a list applies unchanged — indexing starts at `0`, negative indices count from the end, and slicing (including a step, like `nums[::-1]` to reverse) returns a brand-new tuple:

```python
person = ("Ada", 36, True)
print(person[0], person[-1])
```

Output:

```
Ada True
```

The one thing you cannot do is *write*:

```python
person[1] = 40
```

Output:

```
TypeError: 'tuple' object does not support item assignment
```

A tuple has none of the mutating methods a list has — `append`, `insert`, `remove`, `pop`, `sort` — calling one raises an `AttributeError`, because the method simply doesn't exist on a tuple. In fact, a tuple exposes exactly **two** methods, both read-only (`count()` and `index()`, covered in §3.5) — versus the roughly dozen a list carries. That short menu *is* the immutability guarantee, expressed as an API.

One subtlety worth naming once: immutability applies to the tuple's *structure* — which objects it holds and in what order — not necessarily to those objects' own contents. A tuple holding a list can still have that inner list edited; the tuple simply can't swap it out for a different object. For the everyday case of numbers, strings, and other tuples, treat a tuple as fully fixed. To get a "changed" tuple, you build a new one instead of editing the old:

```python
point = (3, 5)
point = point + (7,)   # rebinds point to a NEW tuple; the original is untouched
```

### 3.3 Unpacking and the Swap

**Unpacking** spreads a tuple's values across several variables in one line — the count on each side must match, or Python raises a `ValueError`:

```python
name, age, branch = ("Priya", 21, "Computer Science")
```

This is also how Python swaps two variables with no temporary one — it builds the tuple `(b, a)` completely *before* assigning anything back:

```python
a, b = 5, 10
a, b = b, a
print(a, b)
```

Output:

```
10 5
```

When you only care about the ends, star-unpacking collects the rest into a list — at most one `*` is allowed:

```python
first, *rest = (1, 2, 3, 4)
print(first, rest)
```

Output:

```
1 [2, 3, 4]
```

Unpacking also reads cleanly inside a `for` loop when each element is itself a tuple, and it's why a function that needs to hand back more than one value almost always returns a tuple for the caller to unpack:

```python
pairs = [("Ada", 36), ("Alan", 41)]
for name, age in pairs:
    print(f"{name} is {age}")
```

Output:

```
Ada is 36
Alan is 41
```

### 3.4 Nested Tuples

A tuple can hold other tuples — the natural way to represent one thing made of paired values, like a location's latitude and longitude, or a table of records:

```python
delivery_point = ("Central Library", (12.9716, 77.5946))
print(delivery_point[1][0])
```

Output:

```
12.9716
```

`delivery_point[1]` gets the inner tuple; the second `[0]` reaches inside it — chained indexing, identical in feel to nested lists.

### 3.5 Operations, Methods, and Comparison

```python
print((1, 2) + (3, 4))     # concatenation — builds a new tuple
print(("a",) * 3)          # repetition
print(21 in (21, "gold"))  # membership
```

Output:

```
(1, 2, 3, 4)
('a', 'a', 'a')
True
```

The two read-only methods:

```python
marks = (7, 3, 7, 7, 1)
print(marks.count(7))    # how many times 7 appears
print(marks.index(7))    # position of the first 7 (raises ValueError if absent)
```

Output:

```
3
0
```

Tuples also compare **lexicographically** — element by element, left to right, the same rule dictionaries use for words:

```python
print((1, 2) < (1, 3))     # first elements tie, 2 < 3 decides
print(sorted([("Ada", 95), ("Alan", 72), ("Ada", 20)]))
```

Output:

```
True
[('Ada', 20), ('Ada', 95), ('Alan', 72)]
```

This is why `sorted()` on a list of tuples gives multi-key ordering for free — the first field breaks ties, then the second, and so on.

**Deciding between a list and a tuple comes down to one question: will the collection's membership change?** If items will be added, removed, or reordered over its life — a to-do list, a growing log, a queue — use a **list**. If the group is a fixed-size, fixed-meaning record whose parts stay put — a coordinate, a color, a date, a row — use a **tuple**.

---

## 4. Real-World Application

A function like `divmod(17, 5)` returning `(3, 2)` is packing-then-unpacking in Python's own built-ins — both values travel together and unpack in one line, the same pattern a `def` uses whenever it returns more than one value for the caller to unpack. A ride-hailing app's driver-location updates work the same way: every ping is a `(latitude, longitude)` pair, because neither number means anything alone. And sorting a list of `(name, score)` records by `sorted()` gets multi-key ordering — by name, then score — for free, purely because tuples compare lexicographically.

---

## 5. Worked Example

**Goal:** Build a small table of records as a list of tuples, process it, and see exactly why a tuple is the safer choice for each fixed record than a list would be.

**1. Build the records and total them by unpacking in the loop header.**

```python
records = [("Ada", 95), ("Alan", 88), ("Grace", 95), ("Alan", 72)]

total = 0
for name, score in records:
    total += score
average = total / len(records)
print(average)
```

Output:

```
87.5
```

**2. Use `count()` and `index()` on just the scores.**

```python
scores_only = tuple(score for name, score in records)
print(scores_only.count(95))
print(scores_only.index(95))
```

Output:

```
2
0
```

**3. Sort the records lexicographically — by name, then score.**

```python
ranked = sorted(records)
print(ranked)
```

Output:

```
[('Ada', 95), ('Alan', 72), ('Alan', 88), ('Grace', 95)]
```

**4. Try to "correct" Ada's score in place, the way you might with a list.**

```python
records[0][1] = 100
```

Output:

```
TypeError: 'tuple' object does not support item assignment
```

**5. Do it correctly — the *list* is mutable, so swap in a new tuple.**

```python
records[0] = ("Ada", 100)
print(records[0])
```

Output:

```
('Ada', 100)
```

This is the whole immutability story in one line: we didn't edit a tuple, we replaced one tuple with a different one inside a mutable list — the list holds the changing collection, and each tuple is a fixed record inside it.

*Common mistake: reaching for `records[0][1] = 100` out of habit from working with plain lists. If a field needs to change, either that record should have been a list to begin with, or you build a fresh tuple with the corrected value and slot it back in — never patch a tuple in place.*

---

## 6. Summary

- A **tuple** is an ordered, immutable sequence — it reads like a list (index, slice, loop, `in`) but cannot be changed after creation, and it has only two methods: `count()` and `index()`.
- The **comma** makes a tuple, not the parentheses; a single-element tuple needs a trailing comma, and `tuple(iterable)` builds one from any iterable.
- **Unpacking** spreads a tuple across variables, enables the `a, b = b, a` swap and star-unpacking (`first, *rest = ...`), and is how functions hand back more than one result.
- **Immutability isn't just a restriction** — a tuple's short method list is the guarantee, expressed as an API, that it can be shared without risk of changing unexpectedly.
- Tuples compare **lexicographically**, which makes `sorted()` on a list of tuples do multi-key ordering for free.

Up next: sets — a collection built around a guarantee neither the list nor the tuple gives you: every value in it is unique.

---

*© 2026 Revature · AI Native Engineering — Foundations · Unit 3.2 · Version 1.0*

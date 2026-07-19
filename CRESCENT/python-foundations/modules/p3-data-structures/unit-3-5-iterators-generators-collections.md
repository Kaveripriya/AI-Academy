# Iterators, Generators & Collections

---

## 1. Learning Objectives

By the end of this unit, you will be able to:

✓ Distinguish an iterable from an iterator, and explain the `iter()`/`next()`/`StopIteration` protocol a `for` loop runs underneath.  
✓ Trace what a `for` loop actually does when it walks any collection, and explain why an iterator is one-shot.  
✓ Write a generator function with `yield` and a generator expression, and explain why either can save real memory.  
✓ Use `Counter` to tally items in one line instead of a hand-written counting loop.  
✓ Use `defaultdict` to group or count items without manual key-existence checks.  
✓ Recognize when `namedtuple` beats a plain tuple for readability.

---

## 2. Overview

Every `for` loop you've written this module — over lists, tuples, sets, and dictionaries — has quietly relied on one uniform mechanism you never had to name: the **iterator protocol**. An **iterable** is anything you can loop over; an **iterator** is the separate object that actually does the walking, one value at a time, until there's nothing left to give. This unit opens that mechanism up, then hands you three tools from the `collections` module — `Counter`, `defaultdict`, and `namedtuple` — that are built directly on top of it.

Think of the iterable as a book and the iterator as a bookmark: the book holds the content, the bookmark remembers where you are, and you could have several bookmarks in the same book at once. A list is not itself an iterator — it doesn't remember a "current position." When you loop over a list, Python asks the list for a *fresh* iterator, and that iterator tracks the position instead.

---

## 3. Description

### 3.1 Iterables vs. Iterators

An **iterable** is any object you can loop over — anything you can put after `in` in a `for` loop. Lists, tuples, sets, dictionaries, and strings all qualify. An **iterator** is the object that actually produces the next value on request and remembers where it left off.

You interact with this machinery through two built-in functions: **`iter(obj)`** takes an iterable and returns a fresh iterator positioned at the start; **`next(it)`** takes an iterator and returns its next value, advancing the position by one.

```python
nums = [10, 20, 30]
it = iter(nums)

print(next(it))
print(next(it))
print(next(it))
```

Output:

```
10
20
30
```

Call `next(it)` a fourth time and Python **raises `StopIteration`** — a special signal meaning "the sequence is finished," not `None` and not `-1`. `iter(nums)` also hands back a *separate* object each time; the list itself is untouched, and calling `iter(nums)` again gives a brand-new iterator starting at `10`.

### 3.2 How a `for` Loop Works Underneath

Every `for` loop you've ever written is shorthand for that exact protocol. Writing `for n in nums:` makes Python do this automatically: call `iter(nums)` once; call `next()` repeatedly to get each value; run the loop body; and when `next()` raises `StopIteration`, stop silently — no error ever reaches you. Spelled out with the raw pieces:

```python
it = iter(nums)
while True:
    try:
        n = next(it)
    except StopIteration:
        break
    print(n)
```

This explains behavior you'd otherwise have to memorize: *any* iterable works in a `for` loop uniformly, because the loop only ever speaks `iter()`/`next()` — it doesn't care whether it's walking a list, a set, a dictionary, a file, or a generator.

### 3.3 Iterators Are One-Shot

One subtlety trips people up. An iterator is *itself* iterable — calling `iter()` on an iterator just hands back that same iterator — which is why you can drop an iterator directly into a `for` loop. But an iterator **exhausts**: once `next()` has walked it to `StopIteration`, looping it again yields nothing, because there's nothing left and the position can't rewind.

```python
it = iter([1, 2, 3])
print(list(it))
print(list(it))
```

Output:

```
[1, 2, 3]
[]
```

Contrast that with the underlying list, which you can loop as many times as you like, because each `for` loop asks it for a *fresh* iterator. The rule: **a list is reusable; an iterator is a single pass.** This matters the moment you meet generators, because a generator *is* an iterator — and therefore one-shot too.

### 3.4 Generators — `yield` and Lazy Evaluation

A **generator function** looks like an ordinary function but uses `yield` instead of `return`. Calling it doesn't run the body — it hands back a generator object, a lazy iterator. Each `next()` call runs the function until it hits `yield`, produces that one value, and *pauses there* — freezing all local state — until you ask for the next one.

```python
def countdown(n):
    while n > 0:
        yield n
        n -= 1

for number in countdown(3):
    print(number)
```

Output:

```
3
2
1
```

Because a generator produces values one at a time, on demand, it never builds the whole sequence in memory. The difference isn't small — measure it directly:

```python
import sys

squares_list = [n * n for n in range(1_000_000)]
squares_gen = (n * n for n in range(1_000_000))

print(sys.getsizeof(squares_list), "bytes")
print(sys.getsizeof(squares_gen), "bytes")
```

Output:

```
8448728 bytes
104 bytes
```

The list costs roughly 8 megabytes before you've used a single value from it. The generator costs about the same whether the range were a thousand or a billion, because it isn't storing values — it's storing a plan for producing the next one. For large or unpredictable amounts of data, "compute it when asked" is the difference between a program that runs and one that runs out of memory.

The trade-off: a generator can only be walked through once.

```python
gen = (n for n in range(3))
print(list(gen))
print(list(gen))
```

Output:

```
[0, 1, 2]
[]
```

If you need the values more than once, either materialize them with `list(...)` the first time, or call the generator function again for a fresh one.

### 3.5 The `collections` Module

Python's built-in `list`, `tuple`, `set`, and `dict` cover most needs, but a few patterns are common enough that the standard library ships purpose-built versions in `collections`.

**`Counter`** — a `dict` subclass that tallies occurrences of hashable items in one line, replacing a hand-written `counts[w] = counts.get(w, 0) + 1` loop:

```python
from collections import Counter

grades = ["A", "B", "A", "C", "A", "B"]
tally = Counter(grades)

print(tally)
print(tally.most_common(1))
```

Output:

```
Counter({'A': 3, 'B': 2, 'C': 1})
[('A', 3)]
```

Because a `Counter` *is* a dictionary, everything you already know still applies — an unseen key returns `0` instead of raising `KeyError`. `most_common(n)` returns the `n` highest-count items already sorted, replacing a `sorted(items(), key=...)` line.

**`defaultdict`** — a dictionary that auto-creates a default value the first time a new key is used, instead of raising `KeyError`. You give it a factory function at creation; the two you'll use constantly are `list` (for grouping) and `int` (for counting):

```python
from collections import defaultdict

groups = defaultdict(list)
for name in ["Priya", "Rohan", "Arjun", "Kavya"]:
    groups[name[0]].append(name)
print(dict(groups))

tally = defaultdict(int)
for ch in "banana":
    tally[ch] += 1
print(dict(tally))
```

Output:

```
{'P': ['Priya'], 'R': ['Rohan'], 'A': ['Arjun'], 'K': ['Kavya']}
{'b': 1, 'a': 3, 'n': 2}
```

`defaultdict(list)` replaces the `setdefault(k, []).append(x)` pattern; `defaultdict(int)` replaces `get(k, 0) + 1` — the key is created on first access, so the default already exists by the time you touch it.

**`namedtuple`** — a tuple whose positions also have readable names, so `student[0]` can be written as `student.name` instead. It's still a real tuple: immutable, unpackable, and comparable — just self-documenting:

```python
from collections import namedtuple

Student = namedtuple("Student", ["name", "marks"])
s1 = Student("Priya", 82)

print(s1.name, s1.marks)
```

Output:

```
Priya 82
```

---

## 4. Real-World Application

A live sports score feed updating point by point, not the whole match dumped on you at once, is lazy evaluation in the wild — the next update is generated only as it happens, the same way `countdown()` yields one value instead of pre-computing an entire sequence nobody's asked for yet. A quiz app reporting "most selected wrong answer" is `Counter` tallying every submitted answer in one pass and reading the top one off with `most_common()`. And a function that hands back several pieces of information at once — say, `(success, message, code)` — is a natural fit for `namedtuple`: the caller writes `result.code` instead of decoding what position 2 was supposed to mean.

---

## 5. Worked Example

**Goal:** Take a semester's grade records, count and group them with the `collections` tools, then hit the generator "only once" trap firsthand.

**1. Store the records as `namedtuple`s instead of bare tuples.**

```python
from collections import namedtuple

Record = namedtuple("Record", ["name", "grade"])
records = [
    Record("Priya", "A"), Record("Rohan", "B"), Record("Arjun", "A"),
    Record("Meera", "C"), Record("Kavya", "B"), Record("Sara", "A"),
]

print(records[0].name, records[0].grade)
```

Output:

```
Priya A
```

**2. Count grades with `Counter`, feeding it a generator expression so no temporary list is built.**

```python
from collections import Counter

grade_counts = Counter(r.grade for r in records)
print(grade_counts)
```

Output:

```
Counter({'A': 3, 'B': 2, 'C': 1})
```

**3. Group names by grade with `defaultdict` — no manual key checks.**

```python
from collections import defaultdict

by_grade = defaultdict(list)
for r in records:
    by_grade[r.grade].append(r.name)
print(dict(by_grade))
```

Output:

```
{'A': ['Priya', 'Arjun', 'Sara'], 'B': ['Rohan', 'Kavya'], 'C': ['Meera']}
```

**4. Write a generator that yields only the "A" students, then walk it once.**

```python
def a_grade_students(records):
    for r in records:
        if r.grade == "A":
            yield r.name

top_students = a_grade_students(records)
for name in top_students:
    print(name)
```

Output:

```
Priya
Arjun
Sara
```

**5. Try to loop over `top_students` a second time.**

```python
for name in top_students:
    print(name)
```

Output:

```
(nothing prints)
```

This isn't a bug — `top_students` is the generator object itself, and step 4 already walked it to the end. If you need the names again, either materialize them once with `list(a_grade_students(records))`, or call `a_grade_students(records)` again for a fresh generator.

*Common mistake: assuming a generator behaves like a list you can loop over as many times as you want. A list holds its values and lets you revisit them freely; a generator hands them out once and forgets they ever existed.*

---

## 6. Summary

- An **iterable** is anything loopable; an **iterator** is the object that hands out one value at a time from it, via `iter()` and `next()`, until it raises `StopIteration`.
- A **`for` loop is just this protocol** — it calls `iter()` once, then `next()` repeatedly, stopping silently on `StopIteration`. An **iterator is one-shot**; the underlying iterable is reusable.
- A **generator** — a function using `yield`, or a `(... for ...)` expression — is a lazy iterator that streams values without building them all in memory, which the `sys.getsizeof()` comparison shows directly.
- **`Counter`** tallies occurrences in one line and ranks them with `most_common()`; **`defaultdict`** removes manual key-existence checks for grouping (`list`) or counting (`int`); **`namedtuple`** adds readable names to a tuple's positions.

This closes Part 3's data structures. Part 4 moves to classes and objects — organizing data and the behavior that acts on it together, instead of keeping them separate.

---

*© 2026 Revature · AI Native Engineering — Foundations · Unit 3.5 · Version 1.0*

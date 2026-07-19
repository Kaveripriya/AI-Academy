# Iterators, Generators & Collections

---

## 1. Learning Objectives

By the end of this unit, you will be able to:

✓ Explain the difference between an iterable and an iterator, and what `iter()`/`next()` actually do.  
✓ Explain why a `for` loop never shows you a `StopIteration` error, even though one is always raised.  
✓ Write a generator function with `yield`, and explain why it can save real, measurable memory.  
✓ Use `Counter` to tally items and `defaultdict` to group them without manual key checks.  
✓ Recognise when `namedtuple` beats a plain tuple for readability.

---

## 2. Overview

An **iterable** is anything you can loop over — a list, a string, a dictionary. An **iterator** is the actual mechanism doing the work behind that loop: an object that hands back one value at a time, on request, and knows when there's nothing left to give. Every `for` loop you've written on a list, tuple, set, or dictionary has quietly relied on one without you ever having to look at it directly. This unit opens that mechanism up.

Here's a real-world parallel: picture a made-to-order coffee stand instead of a catering table already stacked with cups. At a catering table, every drink was made in advance, sitting there whether or not anyone ever drinks it — that's a **list**: everything exists in memory right now, all at once. At a made-to-order stand, the barista makes exactly one drink the moment you ask, and not a drop before — that's an iterator in action, and its close relative, the **generator**. Nothing is prepared until it's actually requested.

This unit covers that on-demand model precisely, then introduces three tools from Python's `collections` module — `Counter`, `defaultdict`, and `namedtuple` — that are built directly on top of these same ideas and that you'll reach for constantly once you start working with real data.

---

## 3. Description

### 3.1 Iterables vs. Iterators

An **iterable** is anything you can loop over — a list, tuple, dictionary, set, or string. An **iterator** is the separate object that actually does the work of handing you one value at a time from that iterable, remembering exactly where it left off.

```python
subjects = ["Python", "SQL", "Git"]

order_taker = iter(subjects)   # asks the iterable for an iterator — the "barista"

print(next(order_taker))
print(next(order_taker))
print(next(order_taker))
```

Output:

```
Python
SQL
Git
```

Call `next(order_taker)` a fourth time and you get `StopIteration` — the barista telling you the stand is closed for the day. You've never seen that error appear from a `for` loop, because a `for` loop calls `iter()` once, then calls `next()` in a loop and *quietly catches* `StopIteration` for you to know when to stop. Spelled out with the raw pieces, `for item in subjects:` is doing exactly this underneath:

```python
order_taker = iter(subjects)
while True:
    try:
        item = next(order_taker)
    except StopIteration:
        break
    print(item)
```

The mechanism was always running; the `for` loop just never shows you its plumbing. One more subtlety worth knowing: an iterator is itself iterable — calling `iter()` on `order_taker` just hands back `order_taker` again — which is why you can drop an iterator straight into a `for` loop, not just an iterable.

### 3.2 Generators — Why "Made-to-Order" Actually Matters

A **generator function** is a function that uses `yield` instead of `return`. The first time you call `next()` on it, it runs until it hits `yield`, hands back that one value, and *pauses exactly there* — variables and all — until you ask for the next one. This is called **lazy evaluation**: nothing downstream of where you currently are has been computed yet.

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

Here's the part that's easy to nod along with but not really *feel* until you see numbers: a generator doesn't just save a little memory, it can save an enormous amount, because a list has to hold every value at once while a generator only ever holds the one it just produced.

```python
import sys

squares_list = [n * n for n in range(1_000_000)]      # a list comprehension — builds ALL 1,000,000 values now
squares_gen  = (n * n for n in range(1_000_000))       # a generator expression — builds NONE of them yet

print(sys.getsizeof(squares_list), "bytes")
print(sys.getsizeof(squares_gen), "bytes")
```

Output:

```
8448728 bytes
104 bytes
```

The list costs roughly 8 megabytes before you've even used a single value from it. The generator costs about the same whether the range were a thousand or a billion — because it isn't storing values, it's storing *a plan* for producing the next one on request. That's the entire reason this unit exists: for large or unpredictable amounts of data, "compute it when asked" isn't just an elegant trick, it's the difference between a program that runs and one that runs out of memory.

The trade-off is real, though: a generator can only be walked through once. Once you've asked for every value, asking again gets you nothing — the barista already closed for the day, and there's no reopening the same one.

```python
gen = (n for n in range(3))
print(list(gen))   # consumes it completely
print(list(gen))   # nothing left — already exhausted
```

Output:

```
[0, 1, 2]
[]
```

### 3.3 The `collections` Module

Python's built-in `collections` module packages a few common patterns — several of which lean directly on iteration — into ready-made tools.

**`Counter`** — walks an iterable and tallies how many times each value showed up.

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

**`defaultdict`** — a dictionary that hands back a default value the first time a new key is used, instead of raising `KeyError`. Under the hood, it's iterating over whatever you give it and building groups on the fly, one item at a time.

```python
from collections import defaultdict

groups = defaultdict(list)

for name in ["Priya", "Rohan", "Arjun", "Kavya"]:
    groups[name[0]].append(name)

print(dict(groups))
```

Output:

```
{'P': ['Priya'], 'R': ['Rohan'], 'A': ['Arjun'], 'K': ['Kavya']}
```

**`namedtuple`** — a tuple where each position also has a readable name, so `student[0]` can instead be written as `student.name`.

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

A live cricket score feed updating ball by ball — not the whole match dumped on you at once — is §3.2's lazy evaluation in the wild: the next ball is generated only as it happens, exactly like `countdown()` yielding one value at a time instead of pre-computing an entire match nobody's finished playing yet. An AI chatbot's reply appearing word by word works the same way — each token is produced and sent the moment it's ready, not held back until the whole answer exists.

The `collections` tools show up just as often once you're working with real data: a quiz app reporting "most selected wrong answer" after a test is §3.3's `Counter`, tallying every submitted answer in one pass and reading the top one off directly with `most_common()`. YouTube Studio grouping your uploaded videos by category automatically is the `defaultdict(list)` pattern — each video drops into its category's list the first time that category shows up, with no manual "does this key exist yet?" check anywhere in the code.

---

## 5. Worked Example

**Goal:** Take a semester's grade records, count them, group them, and stream out just the top performers — using the exact tools from this unit, including one deliberate mistake to see the generator "only once" trap firsthand.

**1. Store the raw data.**

```python
records = [
    ("Priya", "A"), ("Rohan", "B"), ("Arjun", "A"),
    ("Meera", "C"), ("Kavya", "B"), ("Sara", "A"),
]
```

**2. Count how many students got each grade.**

```python
from collections import Counter

grades_only = [grade for _, grade in records]
grade_counts = Counter(grades_only)

print(grade_counts)
```

Output:

```
Counter({'A': 3, 'B': 2, 'C': 1})
```

**3. Group student names by grade, with no manual key checks.**

```python
from collections import defaultdict

by_grade = defaultdict(list)

for name, grade in records:
    by_grade[grade].append(name)

print(dict(by_grade))
```

Output:

```
{'A': ['Priya', 'Arjun', 'Sara'], 'B': ['Rohan', 'Kavya'], 'C': ['Meera']}
```

**4. Write a generator that yields only the "A" grade students, one at a time.**

```python
def a_grade_students(records):
    for name, grade in records:
        if grade == "A":
            yield name

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

**5. Now try to loop over `top_students` a second time.**

```python
for name in top_students:
    print(name)
```

Output:

```
(nothing prints)
```

This isn't a bug — `top_students` is the generator object itself, and step 4 already walked it to the end. If you need the names more than once, either convert it once with `list(a_grade_students(records))`, or call `a_grade_students(records)` again to get a brand-new generator to walk through.

*Common mistake: assuming a generator behaves like a list you can loop over as many times as you want. A list holds its values and lets you revisit them freely; a generator hands them out once and forgets they ever existed.*

---

## 6. Summary

- **An iterable** is anything loopable; **an iterator** is the object that hands out one value at a time from it, via `iter()` and `next()`, until it raises `StopIteration`.
- **A `for` loop never shows you `StopIteration`** because it's catching that signal internally — the mechanism was running the whole time, just out of sight.
- **Generator functions use `yield`** to produce values lazily — one at a time, only when asked — which can be the difference between a program that fits in memory and one that doesn't, as the `sys.getsizeof()` comparison in §3.2 showed directly.
- **A generator can only be consumed once.** Once exhausted, it yields nothing more; get a fresh one by calling the generator function again.
- **`Counter`** tallies occurrences in one line; **`defaultdict`** removes manual key-existence checks; **`namedtuple`** adds readable names to a tuple's positions — all three lean on the same iteration model from §3.1.

This closes Part 3's data structures. Part 4 moves to classes and objects — organizing data and the behaviour that acts on it together, instead of keeping them separate.

---

*© 2026 Revature · AI Native Engineering — Foundations · Unit 3.5 · Version 1.0*

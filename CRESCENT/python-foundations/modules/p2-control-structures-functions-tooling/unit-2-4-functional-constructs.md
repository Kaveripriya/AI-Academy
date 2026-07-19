# Functional Constructs

---

## 1. Learning Objectives

By the end of this unit, you will be able to:

✓ Write a `lambda` — and know when it's the right call and when a regular function is actually clearer.  
✓ Explain what a "higher-order function" is, using `map()`, `filter()`, and `sorted(key=...)`.  
✓ Read and write a simple decorator, and explain what `@decorator` is really shorthand for.  
✓ Write a generator with `yield`, and explain why it can handle data a normal function can't.

---

## 2. Overview

Every function you've written so far runs the same way: Python calls it, it runs top to bottom, and it hands back one final answer. This unit is about four ways Python bends that pattern — a function so small it doesn't even get a name, functions that take *other functions* as their input, functions that quietly wrap extra behavior around a function without touching its code, and functions that pause mid-run instead of finishing in one go.

A useful mental model for most of this unit is a photo-editing app on your phone. When you apply a brightness edit to every photo in an album at once, you're not manually opening each photo — you're telling the app "run this one adjustment across all of them." When the app hides everything except photos with a detected face, it's testing each photo and keeping only the ones that pass. That's exactly the shape of `map()` and `filter()`, just applied to numbers and text instead of pixels.

This unit covers lambda functions, higher-order functions (`map()`, `filter()`, `sorted(key=...)`), a light introduction to decorators, and generators. All four show up constantly in real AI/ML code — sorting model predictions by confidence score, filtering out bad data rows, timing how long a training step takes, and streaming a dataset too large to fit in memory all at once.

---

## 3. Description

### 3.1 Lambda (Anonymous) Functions

A **lambda** is a function with no name, written on a single line, meant to be used once and thrown away — like a quick edit you swipe onto one photo and never save as a reusable preset.

```python
square = lambda x: x * x
print(square(6))
```

Output:

```
36
```

Lambdas are almost never written to be called by name like that, though — you'll mostly see them passed *directly into* another function, right where they're needed:

```python
students = [("Priya", 92), ("Rohan", 68), ("Arjun", 81)]

students.sort(key=lambda student: student[1])
print(students)
```

Output:

```
[('Rohan', 68), ('Arjun', 81), ('Priya', 92)]
```

`key=lambda student: student[1]` tells `sort()` which part of each tuple to compare — the mark, at index `1` — without you having to write and name a separate function just for this one use.

**Where the line is.** A lambda can only hold a single expression — no loops, no multiple lines, no `if`/`elif`/`else` chains. That's not a small technical detail; it's a readability guardrail. The moment your one-off logic needs more than a single simple expression, write a normal, named `def` function instead. A lambda crammed with nested conditions to avoid "wasting" a `def` is harder to read, not more efficient — terseness that costs clarity is a bad trade.

### 3.2 Higher-Order Functions

A **higher-order function** is any function that takes another function as an input, or hands one back. `map()`, `filter()`, and `sorted(key=...)` are the three you'll use constantly.

- **`map()`** — runs one function across every item in a sequence, like applying the same brightness edit to every photo in an album:

```python
marks = [78, 85, 92]
percentages = list(map(lambda m: m / 100, marks))
print(percentages)
```

Output:

```
[0.78, 0.85, 0.92]
```

- **`filter()`** — tests every item and keeps only the ones that pass, like a gallery view showing only the photos where a face was detected:

```python
marks = [35, 78, 42, 88, 39]
passing = list(filter(lambda m: m >= 40, marks))
print(passing)
```

Output:

```
[78, 42, 88]
```

- **`sorted(key=...)`** — orders a sequence using whatever property you point it at, without changing the original list:

```python
students = [("Priya", 92), ("Rohan", 68), ("Arjun", 81)]
ranked = sorted(students, key=lambda s: s[1], reverse=True)
print(ranked)
print(students)   # untouched — sorted() always returns a new list
```

Output:

```
[('Priya', 92), ('Arjun', 81), ('Rohan', 68)]
[('Priya', 92), ('Rohan', 68), ('Arjun', 81)]
```

`map()` and `filter()` are honest tools, but they're not always the most readable ones. `list(map(lambda m: m / 100, marks))` and `[m / 100 for m in marks]` (a **list comprehension** — you'll meet this properly in the data structures unit) compute the exact same thing, and most Python developers find the second one easier to read at a glance. `sorted(key=...)`, on the other hand, has no such rival — it's the version you'll reach for constantly.

### 3.3 Decorators (Light Introduction)

A **decorator** wraps a function with extra behavior *before* and/or *after* it runs, without changing a single line inside the function itself — like an app automatically stamping a watermark onto every photo the moment you hit export, without you ever touching the original photo file.

```python
import time

def timer(func):
    def wrapper(*args, **kwargs):
        start = time.time()
        result = func(*args, **kwargs)
        end = time.time()
        print("Took", round(end - start, 4), "seconds")
        return result
    return wrapper

@timer
def slow_greeting():
    time.sleep(1)
    print("Hello from AI Native Engineering!")

slow_greeting()
```

Output:

```
Hello from AI Native Engineering!
Took 1.0006 seconds
```

`@timer` directly above `def slow_greeting():` is not magic syntax — it's shorthand for one line: `slow_greeting = timer(slow_greeting)`. `timer()` receives the original function, builds a new `wrapper()` function that calls it in the middle, and hands that wrapper back. Every time you now call `slow_greeting()`, you're actually calling `wrapper()` — which is why the timing shows up on both sides of the original message.

`wrapper`'s signature is `(*args, **kwargs)`, not empty parentheses, for a reason: `timer` has no idea in advance what the function it's wrapping actually needs. `slow_greeting` here takes zero arguments, but the next function you decorate with `@timer` might take three positional arguments and a keyword one — `*args, **kwargs` collects whatever was passed in and forwards it straight through to `func`, so one `timer` works on *any* function, not just this one.

### 3.4 Generators

A **generator** looks like a normal function but uses `yield` instead of `return`. The difference matters more than the swapped keyword suggests: a normal function runs start to finish and hands back one value, while a generator produces one value, *pauses exactly where it left off*, and waits to be asked for the next one.

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

This is called **lazy evaluation** — nothing is computed until it's actually asked for. Picture a photo app that loads thumbnails only as you scroll, instead of loading every full-resolution photo in a 10,000-image album into memory the instant you open it. A generator gives you exactly that behavior for any sequence of values: you get the next one on demand, and everything after it stays uncomputed until you ask.

---

## 4. Real-World Application

A coding platform's leaderboard, ranking every participant by score the instant a new submission comes in, is `sorted(key=...)` (§3.2) doing exactly what it did to the students in this unit — pointing at one field and ordering by it, with no hand-written comparison logic anywhere.

A payment app that logs how long every single transaction takes, without a single line of timing code inside the actual payment logic, is the decorator pattern from §3.3 at work — `@timer`-style wrapping means the transaction function stays focused on payments, and the timing behavior lives entirely on the outside.

Streaming a long video without downloading the whole file first is the same idea as §3.4's generator: the player asks for the next chunk only when it's actually needed, instead of pulling the entire file into memory upfront — exactly the difference between `yield`-ing one value at a time and returning a fully built list.

---

## 5. Worked Example

**Goal:** Rank students by marks, time how long it takes, and then hit a case where reaching for a lambda is the *wrong* call — to see the readability tradeoff from §3.1 in practice, not just in theory.

**1. Define the timer decorator.**

```python
import time

def timer(func):
    def wrapper(*args, **kwargs):
        start = time.time()
        result = func(*args, **kwargs)
        end = time.time()
        print("Ranking took", round(end - start, 5), "seconds")
        return result
    return wrapper
```

**2. Write the ranking function, decorated with `@timer`.**

```python
@timer
def rank_students(students):
    return sorted(students, key=lambda s: s[1], reverse=True)
```

**3. Call it.**

```python
students = [("Priya", 92), ("Rohan", 68), ("Arjun", 81)]
print(rank_students(students))
```

Output:

```
Ranking took 0.00002 seconds
[('Priya', 92), ('Arjun', 81), ('Rohan', 68)]
```

**4. Now try to also grade each student pass/fail inside the same sort key, using a lambda.**

```python
# Don't do this — cramming an if/else into a lambda to avoid writing a def:
ranked = sorted(
    students,
    key=lambda s: (0 if s[1] >= 40 else 1, -s[1])
)
```

This runs, but you had to stop and think about what `(0 if s[1] >= 40 else 1, -s[1])` even means. That's the readability guardrail from §3.1 showing up for real: the logic outgrew "a single simple expression" the moment it needed a pass/fail condition.

**5. Fix it — pull the logic into a named function instead.**

```python
def sort_key(student):
    name, mark = student
    passed = mark >= 40
    return (0 if passed else 1, -mark)

ranked = sorted(students, key=sort_key)
print(ranked)
```

Output:

```
[('Priya', 92), ('Arjun', 81), ('Rohan', 68)]
```

Same result — but anyone reading `sort_key` six months from now (including you) can tell what it's doing without decoding a one-liner first.

*Common mistake: treating "I could write this as a lambda" as the same question as "should I write this as a lambda." If you need a comment to explain what your lambda is doing, that's the signal to give it a name and a `def` instead.*

---

## 6. Summary

- A **lambda** is a small, unnamed, single-expression function — ideal as a disposable argument to another function, and a readability problem the moment it needs more than one simple expression.
- A **higher-order function** (`map()`, `filter()`, `sorted(key=...)`) takes a function as input to transform, filter, or order data — `sorted(key=...)` is the one you'll reach for constantly; `map()`/`filter()` often have a more readable comprehension-based alternative.
- A **decorator** wraps a function with extra behavior using `@syntax`, without touching the original function's code — `@timer` above a function is shorthand for `func = timer(func)`.
- A **generator** uses `yield` to produce values one at a time, on demand, instead of computing everything upfront — the same trick a photo app uses to load thumbnails only as you scroll.

Next up: organizing code across files — modules, packaging, and the professional tooling every Python project uses.

---

*© 2026 Revature · AI Native Engineering — Foundations · Unit 2.4 · Version 1.0*

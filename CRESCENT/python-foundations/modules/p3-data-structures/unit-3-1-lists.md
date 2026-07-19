# Lists

---

## 1. Learning Objectives

By the end of this unit, you will be able to:

✓ Create a list and access elements using positive and negative indices.  
✓ Slice a list, including with a step, to pull out part of it.  
✓ Explain mutability, and predict when changing a list through one variable affects another.  
✓ Use the core list methods to add, remove, and search for elements.  
✓ Sort a list with `sort()` and `sorted()`, and explain why they behave differently.  
✓ Build nested lists and write a list comprehension.

---

## 2. Overview

Every variable you've used so far has held exactly one value — one mark, one name. Real data rarely comes in ones. A class has thirty students, not one; an order has a dozen items, not one. Python's **list** is what you use to hold many values under a single name, in a specific order.

Think of a list like a whiteboard to-do list stuck on a fridge. You can add a new task at the bottom, cross one out, squeeze an urgent one in at the top, or erase the whole thing and start over — the list is meant to be rewritten. Compare that to a printed grocery receipt: once it's printed, it's fixed, and if you want a different set of items you print a new receipt rather than editing the old one. Lists behave like the whiteboard. (You'll meet the "printed receipt" kind of collection — the tuple — in the next unit, once you've seen exactly why the whiteboard's flexibility is a double-edged sword.)

This unit covers creating and indexing a list, slicing out part of it, changing it in place, the handful of methods you'll use constantly, sorting, nesting lists inside each other, and writing a list comprehension — a compact one-line way to build a list.

---

## 3. Description

### 3.1 Creating and Indexing a List

A list is written as comma-separated values inside square brackets `[ ]`. Each value has a position, called an **index**, starting at `0` — not `1`.

```python
students = ["Priya", "Rohan", "Arjun", "Meera"]

print(students[0])
print(students[2])
```

Output:

```
Priya
Arjun
```

**Negative indices** count backward from the end, so you don't have to know a list's length to reach its last item: `-1` is always the last element, `-2` the second-last.

```python
print(students[-1])
```

Output:

```
Meera
```

### 3.2 Slicing — Taking a Piece of a List

A **slice** pulls out a sub-list without changing the original, using `list[start:stop:step]`. `start` is included; `stop` is not.

```python
print(students[1:3])
print(students[::2])
print(students[::-1])
print(students[0:4:2])
```

Output:

```
['Rohan', 'Arjun']
['Priya', 'Arjun']
['Meera', 'Arjun', 'Rohan', 'Priya']
['Priya', 'Arjun']
```

- `students[1:3]` — from index 1 up to (not including) index 3.
- `students[::2]` — every second element, start to end (`step=2`).
- `students[::-1]` — a step of `-1` walks backward, which is the standard trick for reversing a list.
- `students[0:4:2]` — all three parts together: start at 0, stop before 4, step by 2. `start`/`stop` pick the range; `step` picks which elements inside it you actually keep.

### 3.3 Mutability — and Its Sharp Edge

A number or a string can't be changed in place — `"hi"` is always `"hi"`; to get different text you make a *new* string. A list is different: it's **mutable**, meaning you can change its contents without creating a new list at all.

```python
students[1] = "Kavya"
print(students)
```

Output:

```
['Priya', 'Kavya', 'Arjun', 'Meera']
```

That flexibility is exactly what makes lists useful — and exactly what can bite you. If two variables end up pointing at the *same* list, a change made through one shows up through the other too, because there's only ever one list sitting in memory; both names just point at it.

```python
scores_a = [78, 82, 90]
scores_b = scores_a          # scores_b points to the exact same list, not a copy

scores_b.append(100)
print(scores_a)              # scores_a changed too — nobody touched it directly!
```

Output:

```
[78, 82, 90, 100]
```

This is called **aliasing**, and it's one of the most common sources of "but I never changed that variable!" bugs. If you actually want an independent copy, say so explicitly: `scores_b = scores_a.copy()`.

### 3.4 Core List Methods

| **Method** | **What it does** |
|---|---|
| `append(x)` | Adds `x` to the end of the list. |
| `insert(i, x)` | Inserts `x` at position `i`, shifting everything after it along. |
| `remove(x)` | Removes the first occurrence of the *value* `x` (not a position). |
| `pop(i)` | Removes **and returns** the item at index `i` — the last item if `i` is left out. |
| `extend(iterable)` | Adds every element of another list onto the end of this one. |
| `index(x)` | Returns the position of the first occurrence of `x`. |
| `count(x)` | Returns how many times `x` appears. |
| `clear()` | Empties the list completely, leaving `[]`. |

```python
students.append("Sara")
students.remove("Arjun")
print(students)
```

Output:

```
['Priya', 'Kavya', 'Meera', 'Sara']
```

`remove()` and `pop()` are easy to mix up: `remove("Sara")` deletes by *value* — it searches for that name — while `pop(0)` deletes by *position*, regardless of what's stored there.

### 3.5 Looping Through a List

```python
for name in students:
    print("Student:", name)
```

Output:

```
Student: Priya
Student: Kavya
Student: Meera
Student: Sara
```

`for name in students` reads each element in order and hands it to `name`, one at a time — you never need to touch an index unless you specifically need the position too.

### 3.6 Sorting: `sort()` vs. `sorted()`

`sort()` rearranges the list **in place** — it changes the original and gives back nothing (`None`). `sorted()` leaves the original list exactly as it was and hands you back a **brand-new**, sorted list.

```python
marks = [78, 45, 92, 60]

marks.sort()
print(marks)

print(sorted(marks, reverse=True))
```

Output:

```
[45, 60, 78, 92]
[92, 78, 60, 45]
```

Mixing these up is a classic bug: `marks = marks.sort()` looks reasonable but actually throws your data away, because `sort()`'s return value is `None`. If you need the sorted list *as a value* (to print it, pass it somewhere, etc.), reach for `sorted()`.

The `key` argument controls what gets compared — `sorted(students, key=len)` sorts names by how many characters they have, not alphabetically.

### 3.7 Nested Lists

A list can hold other lists as its elements — the natural way to represent a table, where each inner list is one row.

```python
class_marks = [
    ["Priya", 78],
    ["Rohan", 65],
    ["Arjun", 90],
]

print(class_marks[2][0])
print(class_marks[2][1])
```

Output:

```
Arjun
90
```

`class_marks[2]` reaches the third row (`["Arjun", 90]`); adding a second `[0]` or `[1]` reaches into that row for a specific column.

### 3.8 List Comprehension

A **list comprehension** builds a new list in one line — it's shorthand for a `for` loop that would otherwise take several lines to build up a list one `append()` at a time.

```python
squares = [n * n for n in range(1, 6)]
print(squares)

high_scorers = [name for name, mark in class_marks if mark >= 70]
print(high_scorers)
```

Output:

```
[1, 4, 9, 16, 25]
['Priya', 'Arjun']
```

Read it left to right as "give me `n * n`, for every `n` in that range" — and, when there's an `if` at the end, "...but only keep the ones where this is true."

---

## 4. Real-World Application

A food delivery app's order history, newest first, is almost certainly a plain list under the hood — a new order gets `insert(0, ...)`'d at the front, exactly like §3.4's `insert()`, so everything else just shifts down.

Ever notice two browser tabs of the same shopping cart both update the instant you add an item in one of them? That's §3.3's aliasing in the wild — both tabs are holding a reference to the *same* underlying cart list on the server's side, so a change made through one is visible through the other without either tab "syncing" anything; they were never two separate lists to begin with.

A leaderboard that re-sorts itself the moment a new high score comes in is `sort()` from §3.6, doing exactly what it did in this unit — the same list, rearranged in place, not a new leaderboard rebuilt from scratch each time.

---

## 5. Worked Example

**Goal:** Store five students' marks, compute the class average, find the topper, and along the way, deliberately trigger the aliasing bug from §3.3 so you recognize it if it happens to you later.

**1. Store the data.**

```python
students = ["Priya", "Rohan", "Arjun", "Meera", "Kavya"]
marks = [78, 65, 90, 55, 82]
```

**2. Loop through both lists together.**

```python
for i in range(len(students)):
    print(students[i], "scored", marks[i])
```

Output:

```
Priya scored 78
Rohan scored 65
Arjun scored 90
Meera scored 55
Kavya scored 82
```

**3. Compute the class average.**

```python
average = sum(marks) / len(marks)
print("Class average:", average)
```

Output:

```
Class average: 74.0
```

**4. Find the topper using the index of the highest mark.**

```python
topper_index = marks.index(max(marks))
print("Topper:", students[topper_index])
```

Output:

```
Topper: Arjun
```

**5. Now trigger the aliasing bug on purpose.** Suppose you want to try a "what-if a bonus mark is added" scenario, so you grab what looks like a safe copy:

```python
trial_marks = marks          # looks like a copy — it is NOT
trial_marks.append(100)

print("Trial:", trial_marks)
print("Original marks:", marks)   # marks changed too!
```

Output:

```
Trial: [78, 65, 90, 55, 82, 100]
Original marks: [78, 65, 90, 55, 82, 100]
```

**6. Fix it with an actual copy.**

```python
trial_marks = marks.copy()
trial_marks.append(100)

print("Trial:", trial_marks)
print("Original marks:", marks)   # unaffected this time
```

Output:

```
Trial: [78, 65, 90, 55, 82, 100]
Original marks: [78, 65, 90, 55, 82]
```

*Common mistake: assuming `list index [5]` is valid on a 5-item list. Indices run `0` to `4` for five items, not `1` to `5` — `students[5]` raises `IndexError: list index out of range`. And, separately: assuming `new_list = old_list` makes a copy. It doesn't — it makes a second name for the same list.*

---

## 6. Summary

- **Lists** are ordered, mutable collections written inside `[ ]`, indexed from `0`, with negative indices counting from the end.
- **Slicing** (`list[start:stop:step]`) reads out part of a list without touching the original.
- **Mutability cuts both ways** — it's what makes `append()`/`remove()`/sorting-in-place possible, and it's also why two variables pointing at the same list can silently affect each other (aliasing). Use `.copy()` when you actually need an independent copy.
- **`sort()` changes the list and returns nothing; `sorted()` returns a new list and leaves the original alone** — confusing the two is a very common bug.
- **Nested lists** model tables of records; **list comprehensions** build a filtered list in one readable line instead of a multi-line loop.

Next up: tuples — a collection that looks similar to a list but makes exactly the opposite trade-off: no mutability, in exchange for a guarantee that its contents can never change out from under you.

---

*© 2026 Revature · AI Native Engineering — Foundations · Unit 3.1 · Version 1.0*

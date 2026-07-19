# Loops

---

## 1. Learning Objectives

By the end of this unit, you will be able to:

✓ Write a `while` loop, and explain the one habit that keeps it from running forever by accident.  
✓ Write a `for` loop to step through a sequence, including with `range()`.  
✓ Use `enumerate()` and `zip()` to loop with position numbers or paired sequences.  
✓ Use `break` and `continue` to change a loop's behavior from inside it.  
✓ Write a nested loop and trace what order it actually runs in.

---

## 2. Overview

Last unit's `if` statement makes a decision once and moves on. A **loop** is what you reach for when the same decision, or the same action, needs to happen over and over — checking every name on a roster, not just the first one.

Picture a security guard doing rounds in a building at night. The guard doesn't check one door and go home — they walk the same route, checking every door, and they keep doing that until their shift ends. That's a loop: one block of instructions, repeated, until something tells it to stop.

Python gives you two ways to set that up. A `while` loop is like a guard who keeps patrolling *until a certain time* — you don't know in advance exactly how many rounds that'll take, only the condition that ends it. A `for` loop is like a guard who's handed a specific list of ten doors to check — you already know exactly what you're stepping through, in order, once each.

This unit covers both loop types, the tools that make looping practical (`range()`, `enumerate()`, `zip()`), and `break`/`continue` — two keywords that let you change a loop's path from the inside, mid-patrol.

---

## 3. Description

### 3.1 The `while` Loop

A `while` loop keeps re-running its block for as long as its condition stays `True` — checked fresh, every single time, before each round.

```python
attempts = 0

while attempts < 3:
    print("Attempt number:", attempts + 1)
    attempts = attempts + 1
```

Output:

```
Attempt number: 1
Attempt number: 2
Attempt number: 3
```

**The one habit that matters more than any syntax rule here:** something inside the loop must eventually make the condition `False`. Above, that's `attempts = attempts + 1` — without it, `attempts` would stay `0` forever, `attempts < 3` would never stop being `True`, and the loop would run until you forcibly stop the program. This is called an **infinite loop**, and it is Python's single most common beginner bug — not because the syntax is hard, but because it's easy to write the *check* and forget the *update*.

Not every loop that never checks a shrinking condition is a mistake, though. `while True:` is a deliberate, common pattern — a guard who patrols indefinitely until something *specific* happens, not on a timer. It only works safely when paired with a `break` somewhere inside that actually fires: "keep asking the user for input until they type something valid" is naturally a `while True:` with a `break` the moment the input passes a check, not a bug to fix.

### 3.2 The `for` Loop

A `for` loop runs its block once for each item in a sequence — a list, a string, anything you can step through — without you having to manage a counter by hand.

```python
subjects = ["Maths", "Physics", "Python"]

for subject in subjects:
    print("Studying:", subject)
```

Output:

```
Studying: Maths
Studying: Physics
Studying: Python
```

`subject` is the **loop variable** — it takes on each value in `subjects` in turn, one per pass, and it's gone once the loop finishes. Notice there's no condition to accidentally get wrong here: the loop simply ends when it's checked every door on the list. That's the trade-off from the overview — a `for` loop trades the *flexibility* of "stop whenever some condition says so" for the *safety* of "there is no way to forget to make this stop."

### 3.3 `range()` — Looping a Fixed Number of Times

Sometimes you want repetition by *count*, not by stepping through an existing list. `range()` generates a sequence of numbers for exactly that:

```python
for i in range(5):
    print("Round", i)
```

Output:

```
Round 0
Round 1
Round 2
Round 3
Round 4
```

`range()` takes up to three arguments, and it's worth knowing all three because you'll see all three used:

| Form | Meaning | Example | Produces |
|---|---|---|---|
| `range(stop)` | Start at 0, stop *before* this number | `range(5)` | 0, 1, 2, 3, 4 |
| `range(start, stop)` | Start here, stop *before* the second number | `range(2, 6)` | 2, 3, 4, 5 |
| `range(start, stop, step)` | Start here, stop before the second, count by `step` each time | `range(0, 10, 2)` | 0, 2, 4, 6, 8 |

### 3.4 `enumerate()` and `zip()`

- **`enumerate()` — position and value together.** Sometimes you need to know *where* you are in a sequence, not just what the current item is:

```python
subjects = ["Maths", "Physics", "Python"]

for position, subject in enumerate(subjects, start=1):
    print(position, "-", subject)
```

Output:

```
1 - Maths
2 - Physics
3 - Python
```

`start=1` just tells `enumerate()` to begin counting at 1 instead of its default of 0 — useful whenever "item 1" should mean the first item, not the zeroth.

- **`zip()` — walking two sequences side by side.** If you have two related lists and want to process matching pairs, `zip()` steps through both at once:

```python
subjects = ["Maths", "Physics", "Python"]
marks = [78, 85, 92]

for subject, mark in zip(subjects, marks):
    print(subject, ":", mark)
```

Output:

```
Maths : 78
Physics : 85
Python : 92
```

Think of `enumerate()` as numbering a guard's rounds, and `zip()` as walking two hallways together, door for door, so you can compare what's behind each pair. One behavior worth knowing before it surprises you: if the two sequences aren't the same length, `zip()` stops as soon as the *shorter* one runs out — pairing a 3-item list with a 5-item list quietly gives you only 3 pairs, no error, no warning.

### 3.5 `break`, `continue`, and Nested Loops

- **`break` — end the patrol early.** `break` stops the loop immediately, even if its condition would otherwise still allow more rounds:

```python
for number in range(1, 10):
    if number == 5:
        break
    print(number)
```

Output:

```
1
2
3
4
```

- **`continue` — skip this one door, keep patrolling.** `continue` skips the rest of the *current* pass only, and moves straight on to the next one — the loop itself keeps running:

```python
for number in range(1, 6):
    if number == 3:
        continue
    print(number)
```

Output:

```
1
2
4
5
```

- **Nested loops — a floor of hallways.** A loop can contain another loop. This is the natural shape for grid-like data: the outer loop picks a floor, the inner loop walks every door on that floor, and the inner loop finishes completely before the outer loop moves to the next floor.

```python
for floor in range(1, 3):
    for room in range(1, 3):
        print(f"Floor {floor}, Room {room}")
```

Output:

```
Floor 1, Room 1
Floor 1, Room 2
Floor 2, Room 1
Floor 2, Room 2
```

- **The loop `else` clause (good to recognize, rarely used).** A `for` or `while` loop can carry an `else` block, which runs only if the loop finished normally — that is, it never hit a `break`. You won't reach for this often, but you should recognize it if you see it in someone else's code.

---

## 4. Real-World Application

An online exam timer is a `while` loop wearing a UI: it keeps re-checking "is time remaining > 0?" every second — exactly §3.1's "patrol until a condition says stop" — and auto-submits your answers the instant that condition finally turns `False`.

A UPI app listing your last 20 transactions is a `for` loop: it steps through a known, fixed list exactly once each, in order, the same shape as §3.2's "handed a specific list of doors."

And a search bar that returns the instant it finds a match, instead of grinding through every remaining record, is `break` (§3.5) doing its job — stopping the moment the answer is found instead of wastefully finishing the patrol.

---

## 5. Worked Example

**Goal:** Build a simple login-attempt limiter, deliberately break it with the classic infinite-loop mistake, then fix it — the same bug you are most likely to write yourself in your first few weeks of looping.

**1. Write the limiter — but forget to update the counter.**

```python
correct_password = "python123"
attempts = 0
max_attempts = 3

while attempts < max_attempts:
    guess = "wrong_guess"          # standing in for real user input
    if guess == correct_password:
        print("Access granted.")
        break
    print("Incorrect password. Try again.")
    # attempts = attempts + 1   <-- missing on purpose
```

If you ran this for real, `attempts` would stay `0` forever, `attempts < max_attempts` would never become `False`, and "Incorrect password. Try again." would print without end — an infinite loop, exactly as described in §3.1.

**2. Fix it by updating the condition's variable inside the loop.**

```python
correct_password = "python123"
attempts = 0
max_attempts = 3

while attempts < max_attempts:
    guess = "wrong_guess"
    if guess == correct_password:
        print("Access granted.")
        break
    attempts = attempts + 1
    print(f"Incorrect password. {max_attempts - attempts} attempt(s) left.")
```

Output:

```
Incorrect password. 2 attempt(s) left.
Incorrect password. 1 attempt(s) left.
Incorrect password. 0 attempt(s) left.
```

**3. Confirm `break` still works for the success path.** Change `guess = "wrong_guess"` to `guess = "python123"` and rerun:

Output:

```
Access granted.
```

The loop exits immediately via `break` on the very first check — it never even reaches the `attempts = attempts + 1` line, because success doesn't need to count against the attempt limit.

*Common mistake: writing the loop's stopping condition correctly, then forgetting that the condition only gets re-checked if something inside the loop actually changes the value it depends on. A `while` loop doesn't get smarter over time — it just keeps re-asking the same question until the answer changes.*

---

## 6. Summary

- **`while`** repeats a block for as long as its condition stays `True` — you are responsible for making sure something inside the loop eventually makes that condition `False`, or you get an infinite loop.
- **`for`** repeats a block once per item in a known sequence — there's no condition to get wrong, because the loop naturally ends when the sequence runs out.
- **`range()`** generates a sequence of numbers on demand, using `start`, `stop`, and an optional `step`, for counting-based repetition.
- **`enumerate()`** hands you the position and the value together; **`zip()`** walks two or more sequences side by side, pairing up matching items.
- **`break`** ends a loop immediately; **`continue`** skips only the current pass and keeps the loop going.

Next up: functions — how to package a block of logic under a name, so you can reuse it by calling it instead of retyping or re-copying it.

---

*© 2026 Revature · AI Native Engineering — Foundations · Unit 2.2 · Version 1.0*

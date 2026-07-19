# Conditionals

---

## 1. Learning Objectives

By the end of this unit, you will be able to:

✓ Write a single `if` statement that runs a block of code only when a condition is `True`.  
✓ Build multi-branch decisions with `if`/`elif`/`else`, choosing exactly one path out of several.  
✓ Explain how indentation defines which statements belong to a branch in Python.  
✓ Combine comparisons with `and`, `or`, and `not` to form richer conditions.  
✓ Nest one conditional inside another, and judge when nesting helps or hurts readability.  
✓ Rewrite a simple value-choosing `if`/`else` as a conditional (ternary) expression.

---

## 2. Overview

Every program you've written so far runs top to bottom, one statement after another, no matter what. Real software has to *react*: a login screen behaves differently for the right password than the wrong one, and a task tracker sorts a job into "urgent," "soon," or "whenever." A **conditional** gives you that branching — it asks a yes/no question about your data and chooses which statements to run based on the answer. That question is always a boolean expression, one that evaluates to `True` or `False`, exactly the values you met with comparison operators in the last unit.

This unit covers the `if` statement, multi-branch `if`/`elif`/`else` decisions, combining conditions with `and`/`or`/`not`, nesting one conditional inside another, and the compact ternary expression for simple two-value choices.

---

## 3. Description

### 3.1 The `if` Statement

The simplest conditional is a single `if`: a header line ending in a colon, followed by an indented block that runs only when the condition is `True`.

```python
temperature = 30

if temperature > 25:
    print("It is warm.")
    print("Consider a lighter jacket.")

print("Done checking.")
```

Output:

```
It is warm.
Consider a lighter jacket.
Done checking.
```

Read it literally: "if `temperature > 25` is `True`, run the indented block." Because `30 > 25`, both indented lines run. Had `temperature` been `20`, Python would skip the block and jump straight to `print("Done checking.")`, which isn't indented and therefore isn't part of the `if`.

Any value that's `True` or `False` can serve as the condition, so you can test a boolean variable directly:

```python
is_raining = True

if is_raining:
    print("Bring an umbrella.")
```

Output:

```
Bring an umbrella.
```

`if is_raining:` says the same thing as `if is_raining == True:`, more cleanly — the value is already a boolean, so comparing it to `True` just asks the same question twice.

### 3.2 Indentation Is the Structure

Where many languages use braces `{ }` to group statements, Python uses **indentation** — and that indentation isn't decoration, it's the grammar. The standard is four spaces per level, and every line in a block must be indented the same amount.

```python
score = 55

if score >= 50:
    print("You passed.")       # part of the if block
    print("Well done.")        # still part of the if block
print("Results recorded.")     # NOT in the block — always runs
```

Output:

```
You passed.
Well done.
Results recorded.
```

Mixing indentation, or forgetting it, produces an `IndentationError`. The shape of the code on the page matches the logic, so treat indentation with the same care you give the condition itself.

### 3.3 `if` / `else` — Two Paths

When you want one thing to happen on `True` and a different thing on `False`, add an `else`. Exactly one of the two blocks runs — never both, never neither. `else` has no condition of its own; it's the catch-all for everything the `if` didn't cover.

```python
age = 16

if age >= 18:
    print("You may vote.")
else:
    print("You are too young to vote.")
```

Output:

```
You are too young to vote.
```

### 3.4 `if` / `elif` / `else` — Multi-Branch Decisions

For more than two possibilities, chain conditions with `elif` ("else if"). Python checks each condition **in order, top to bottom**, and runs the block for the *first* one that's `True`; once a branch matches, the rest are skipped entirely. An optional final `else` handles the case where none matched.

```python
grade = 78

if grade >= 90:
    print("A")
elif grade >= 80:
    print("B")
elif grade >= 70:
    print("C")
else:
    print("Below C")
```

Output:

```
C
```

A `grade` of `78` fails `>= 90` and `>= 80`, matches `>= 70`, and prints `C`. Notice the code never had to write `grade >= 70 and grade < 80` — because the `>= 80` branch above already handled everything from 80 up, by the time execution reaches the `>= 70` test it's already known the grade is under 80. Three rules follow from this top-to-bottom evaluation:

- **Order matters.** Put the most specific or most common conditions first; if two conditions could both be true, the earlier one wins and the later one never runs.
- **Only one branch executes**, even if a later `elif` would also have been `True`.
- **`else` is optional.** Leave it off for "do nothing when nothing matches," but including it guarantees a path always runs — often safer, since it catches values you didn't anticipate.

### 3.5 Compound Conditions — `and`, `or`, `not`

A condition doesn't have to be a single comparison. Combine comparisons with the logical operators to express richer tests: `and` is `True` only when *both* sides are true, `or` is `True` when *at least one* side is, and `not` flips a boolean.

```python
age = 25
has_ticket = True
logged_in = False

if age >= 18 and has_ticket:
    print("Admitted to the show.")

if not logged_in:
    print("Please log in first.")
```

Output:

```
Admitted to the show.
Please log in first.
```

"You may enter if you are an adult *and* you hold a ticket" reads almost like the English sentence, capturing in one branch what would otherwise need clumsy nesting. Two practical habits: group with parentheses when precedence is unclear — `if (a > 0 and b > 0) or c == 0:` — and remember a range check reads naturally as a chained comparison, `if 0 <= score <= 100:`, which is Python's shorthand for `score >= 0 and score <= 100`.

### 3.6 Nested Conditionals

The block inside an `if` can contain anything — including another `if`. Putting one conditional inside another is called **nesting**, and each level adds a step of indentation.

```python
logged_in = True
is_admin = False

if logged_in:
    if is_admin:
        print("Welcome, administrator.")
    else:
        print("Welcome, user.")
else:
    print("Access denied. Please log in.")
```

Output:

```
Welcome, user.
```

Nesting fits when the second decision only makes sense *after* the first is answered — there's no point asking "are you an admin?" about someone who isn't logged in at all. But every level shifts the code right and adds a fact you have to hold in your head. Often a nested test flattens into one combined condition:

```python
# Nested
if logged_in:
    if is_admin:
        print("Welcome, administrator.")

# Flattened — same result, easier to read
if logged_in and is_admin:
    print("Welcome, administrator.")
```

Prefer the flatter version when both conditions are simply required. Reserve genuine nesting for when the inner decision has its own `else` that the outer level doesn't share.

### 3.7 The Conditional (Ternary) Expression

Sometimes an `if`/`else` exists only to pick between two *values*. Python offers a compact one-line form for that case, called the **conditional expression** (informally, the "ternary operator"). Its shape reads almost like English:

```
value_if_true if condition else value_if_false
```

"Give me `value_if_true` if `condition` is true, else `value_if_false`."

```python
age = 20
status = "adult" if age >= 18 else "minor"
print(status)
```

Output:

```
adult
```

The key distinction: a plain `if`/`else` is a **statement** — it directs what happens, and produces no value of its own. A conditional expression *evaluates to a value*, so you can use it anywhere a value is expected, including straight inside a `print()` call:

```python
number = 7
print("even" if number % 2 == 0 else "odd")
```

Output:

```
odd
```

That line never creates a `status` variable at all — the ternary's result flows straight into `print()`. Use it only for simple two-way value choices that fit comfortably on one line; chaining several ternaries together is possible but quickly becomes unreadable — when you feel that urge, a full `if`/`elif`/`else` chain is the clearer choice.

---

## 4. Real-World Application

A login screen is exactly §3.3's two-path branch in action — one message for the right password, a different one for the wrong one, never both. A thermostat runs on §3.4's multi-branch pattern: turn the heat on below a target temperature, off above it, holding steady in between.

A task tracker sorting a job into "urgent," "soon," or "whenever" based on its due date — the running example this whole unit builds toward — is nothing more than the same `if`/`elif`/`else` chain from §3.4, applied to a priority number instead of a temperature.

---

## 5. Worked Example

**Goal:** Build a task classifier that reads a task's priority and prints how urgent it is, then extend it to also check whether the task is blocked.

**1. Read the priority and classify it.**

```python
priority = int(input("Enter task priority (1-3): "))

if priority == 1:
    print("Urgent — do it now.")
elif priority == 2:
    print("Soon — do it today.")
elif priority == 3:
    print("Whenever — no rush.")
else:
    print("Unknown priority. Please enter 1, 2, or 3.")
```

Output (entering `2`):

```
Enter task priority (1-3): 2
Soon — do it today.
```

**2. Step through what happened.** `input()` returns text, so `int(...)` converts it to a number before the comparisons run. Python tested `priority == 1` (false, skip), then `priority == 2` (true) — it printed `"Soon — do it today."` and skipped every branch below, including the `else`.

**3. Try an out-of-range value.** Enter `9` instead, and all three `==` tests fail, so the final `else` catches it — the program always responds sensibly rather than doing nothing silently. That's exactly why the `else` earns its place here.

**4. Extend it: add a blocked check that overrides everything else.**

```python
priority = int(input("Enter task priority (1-3): "))
is_blocked = input("Is this task blocked? (yes/no): ") == "yes"

if is_blocked:
    print("Blocked — resolve dependency first.")
elif priority == 1:
    print("Urgent — do it now.")
elif priority == 2:
    print("Soon — do it today.")
elif priority == 3:
    print("Whenever — no rush.")
else:
    print("Unknown priority. Please enter 1, 2, or 3.")
```

Output (priority `1`, blocked `yes`):

```
Enter task priority (1-3): 1
Is this task blocked? (yes/no): yes
Blocked — resolve dependency first.
```

Because Python checks conditions top to bottom and stops at the first match, putting `is_blocked` first guarantees the blocked message wins regardless of priority — exactly the "order matters" rule from §3.4.

*Common mistake: putting the blocked check last instead of first. Since only the first matching branch runs, an urgent-but-blocked task would incorrectly print "Urgent — do it now." if the priority check came before the blocked check.*

---

## 6. Summary

- A conditional runs a block of code based on a boolean condition: `if` runs its block only when the condition is `True`, and `else` covers the `False` case.
- Indentation is not just style in Python — it defines which statements belong to which branch, and inconsistent indentation raises an `IndentationError`.
- `if`/`elif`/`else` checks conditions top to bottom and runs only the first branch that's `True` — order matters, and later branches are skipped once one matches.
- Conditions can be compound: `and` requires both sides true, `or` requires at least one, and `not` flips a boolean.
- Nesting puts a decision inside a decision — keep it shallow, and flatten "both must be true" cases with `and`.
- The conditional expression `value_if_true if condition else value_if_false` produces a value in one line, best used for simple two-way value choices.

Up next: loops — how Python repeats a block of code instead of running the same decision once.

---

*© 2026 Revature · AI Native Engineering — Foundations · Unit 2.1 · Version 1.0*

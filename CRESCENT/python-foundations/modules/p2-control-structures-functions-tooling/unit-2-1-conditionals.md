# Conditionals

---

## 1. Learning Objectives

By the end of this unit, you will be able to:

✓ Write single and multi-branch decisions using `if`, `elif`, and `else`.  
✓ Nest conditionals when a decision genuinely depends on more than one layer, and know when nesting has gone too far.  
✓ Combine comparisons with `and`, `or`, and `not` inside one condition — and catch the bug that happens when you mix them up.  
✓ Write a compact ternary expression for a simple two-outcome decision.

---

## 2. Overview

Every unit up to now has been about producing one fixed result — build this string, compute this total. Real programs don't work that way. A checkout page decides whether to show "Free Delivery" based on your cart total; a login page decides whether to let you in based on your password. Python makes decisions like this with a **conditional** — code that runs one block or another, never both, based on whether something is true or false.

Picture an airport gate agent scanning boarding passes. She checks one thing at a time, in a fixed order — priority tag first, then frequent-flyer status, then everyone else — and the moment a passenger matches a category, she waves them into that lane and moves on. She never re-checks a passenger who already got waved through, and she never sends anyone down two lanes at once. That's exactly how Python evaluates a conditional: check conditions in order, stop at the first match, run only that one block.

This unit covers `if`/`elif`/`else`, nesting one decision inside another, combining conditions with `and`/`or`/`not`, and the compact one-line ternary form for simple either/or choices.

---

## 3. Description

### 3.1 `if` / `elif` / `else`

**A single-branch decision.** An `if` runs its block only when the condition evaluates to `True` — nothing happens otherwise:

```python
cart_total = 150

if cart_total < 199:
    print("Add ₹49 more for free delivery")
```

Output:

```
Add ₹49 more for free delivery
```

**A multi-branch decision.** `elif` ("else if") checks additional conditions in order, and `else` catches whatever matches none of them — like the gate agent's fallback lane for everyone who isn't priority or frequent-flyer:

```python
frequent_flyer_tier = "silver"

if frequent_flyer_tier == "platinum":
    print("Boarding Group 1")
elif frequent_flyer_tier == "gold":
    print("Boarding Group 2")
elif frequent_flyer_tier == "silver":
    print("Boarding Group 3")
else:
    print("Boarding Group 4")
```

Output:

```
Boarding Group 3
```

Python checks each condition top to bottom and **stops at the first one that's `True`.** Even if a passenger somehow matched two categories, only the first matching branch runs — the rest are never even checked.

**Indentation is the block.** Python has no `{ }` to mark where a block starts and ends — the indentation itself does that job. Line up two lines under an `if` at the same indent level (four spaces is the standard) and they're both part of that block; mix tabs and spaces, or indent one line differently from its neighbor, and Python raises an `IndentationError` before your code even runs.

One more habit worth building early: if a variable already holds `True` or `False`, test it directly — `if is_raining:` — instead of writing `if is_raining == True:`. Both work, but the second is redundant; the value is already a boolean, so comparing it to `True` just asks the same question twice.

### 3.2 Nested Conditionals

A **nested conditional** is a conditional placed inside another conditional's block — for when a decision only makes sense after an earlier decision has already been settled. It's like a gate agent who, after waving someone into the priority lane, still checks whether they're carrying an oversized bag before letting them board:

```python
has_priority_ticket = True
checked_bag_count = 3

if has_priority_ticket:
    if checked_bag_count <= 2:
        print("Board now, Group 1")
    else:
        print("Priority lane, but excess baggage — see counter first")
else:
    print("Wait for your assigned group")
```

Output:

```
Priority lane, but excess baggage — see counter first
```

**Readability has a limit.** One layer of nesting is fine. Two is still readable. Beyond that, most people lose track of which `else` belongs to which `if`. When you notice yourself nesting three or four levels deep, that's usually a sign the conditions should be combined with `and` instead — which is exactly what the next section does with this same example.

### 3.3 Combining Conditions — `and`, `or`, `not`

`and`, `or`, and `not` let one `if` evaluate more than one condition at a time, often replacing a nested conditional entirely:

```python
has_priority_ticket = True
checked_bag_count = 3

if has_priority_ticket and checked_bag_count <= 2:
    print("Board now, Group 1")
else:
    print("Not yet — check ticket tier or baggage limit")
```

Output:

```
Not yet — check ticket tier or baggage limit
```

This one line does the same job as the nested version in §3.2, more compactly — `and` only lets the first branch run when **both** conditions are `True`.

- **`and`** — every condition must be `True`.
- **`or`** — at least one condition must be `True`.
- **`not`** — flips `True` to `False` and back.

Mixing up `and` and `or` is one of the most common real bugs in conditional logic, because both read like ordinary English and the wrong one still "sounds right":

```python
frequent_flyer_tier = "none"
has_priority_ticket = True

# Bug: this grants Group 1 to ANYONE with a priority ticket,
# even a passenger with no frequent-flyer status at all.
if frequent_flyer_tier == "platinum" or has_priority_ticket:
    print("Boarding Group 1")
```

Output:

```
Boarding Group 1
```

That's the wrong result for a passenger with no elite status — the rule almost certainly meant `and` a specific ticket type is present, not "either one is enough." Whenever a rule requires two things to hold *at the same time*, reach for `and`; the moment you see an `or` granting access more easily than you intended, that's the line to re-read first.

**Two habits worth adopting now.** First, when a condition combines `and`/`or` in a non-obvious way, wrap the parts in parentheses — `if (age >= 18 and has_ticket) or is_staff:` — even though Python doesn't require it. It costs nothing and removes any doubt about which operator applies to which part. Second, a range check reads more naturally as a chained comparison than as two conditions joined by `and`: `if 0 <= score <= 100:` means exactly `score >= 0 and score <= 100`, just shorter and closer to how you'd say it out loud.

### 3.4 The Ternary (Conditional) Expression

For a simple choice between exactly two values, Python offers a one-line form instead of a full `if`/`else` block:

```python
seats_available = 0
status = "Sold Out" if seats_available == 0 else "Available"
print(status)
```

Output:

```
Sold Out
```

The pattern reads left to right as *value-if-true*, `if`, *condition*, `else`, *value-if-false*. It's a great fit for exactly two outcomes and nothing else — the instant a decision needs a third outcome or extra logic, a full `if`/`elif`/`else` stays far more readable than trying to force it into one line.

The distinction underneath this is worth naming: a plain `if`/`else` is a **statement** — it directs what the program does next, and doesn't produce a value you can use elsewhere. A ternary is an **expression** — it evaluates to a value, so you can drop it directly wherever a value is expected, including straight inside a `print()` call:

```python
number = 7
print("even" if number % 2 == 0 else "odd")
```

Output:

```
odd
```

That line never creates a `status` variable at all — the ternary's result flows straight into `print()`.

---

## 4. Real-World Application

An airport's own departure board runs on exactly §3.1's chain — boarding groups are checked and called in a fixed order, and a passenger gets placed into the first group they qualify for, never two at once.

Spam filters lean on §3.3 instead of a single check: real filters combine dozens of signals — sender reputation, flagged words, attachment type — with `and`/`or`, because "spam" is almost never decided by one signal alone. Get the `and`/`or` choice wrong there and you either flood inboxes with junk or bury real mail in the spam folder — the exact class of bug this unit's worked example walks through.

A product page's "In Stock" / "Out of Stock" tag is a good fit for §3.4's ternary — one condition, exactly two possible labels, no reason to write a full `if`/`else` block for it.

A result portal turning a numeric score into a letter grade leans on §3.1's "order matters" rule directly: a chain like `if score >= 90: "A"`, `elif score >= 80: "B"`, `elif score >= 70: "C"` never needs to write `score >= 70 and score < 80` for the `"C"` branch — by the time execution reaches it, the two branches above have already claimed everything 80 and up, so whatever's left is correctly under 80.

---

## 5. Worked Example

**Goal:** Build a boarding-group assigner, introduce the classic `and`/`or` bug on purpose, then fix it — the same mistake airlines' own booking systems have to guard against.

**1. Define a passenger's details.**

```python
frequent_flyer_tier = "gold"
has_priority_ticket = False
checked_bag_count = 1
```

**2. Write the eligibility rule for Group 1 boarding.**

```python
if frequent_flyer_tier == "platinum" and has_priority_ticket:
    print("Boarding Group 1")
else:
    print("Not Group 1 — check next tier")
```

Output:

```
Not Group 1 — check next tier
```

That's correct: this passenger is gold tier, not platinum, and doesn't hold a priority ticket, so they rightly miss Group 1.

**3. Introduce the bug — swap `and` for `or`.**

```python
if frequent_flyer_tier == "platinum" or has_priority_ticket:
    print("Boarding Group 1")
else:
    print("Not Group 1 — check next tier")
```

Output:

```
Not Group 1 — check next tier
```

No visible difference yet — this passenger still fails both conditions either way, which is exactly why this bug is dangerous: it hides until the right input exposes it.

**4. Expose the bug with a different passenger.**

```python
frequent_flyer_tier = "none"
has_priority_ticket = True

if frequent_flyer_tier == "platinum" or has_priority_ticket:
    print("Boarding Group 1")
else:
    print("Not Group 1 — check next tier")
```

Output:

```
Boarding Group 1
```

A passenger with *no* frequent-flyer status just got Group 1 boarding, because `or` only needed one condition to hold — and a priority ticket alone was enough. If the real rule was "platinum tier **and** a priority ticket," this is a genuine incorrect result, not a matter of style.

**5. Fix it — restore `and`.**

```python
if frequent_flyer_tier == "platinum" and has_priority_ticket:
    print("Boarding Group 1")
else:
    print("Not Group 1 — check next tier")
```

Output:

```
Not Group 1 — check next tier
```

*Common mistake: reaching for `or` because a rule sounds like "either of these matters," when it actually means "both of these are required." Read the rule again as "must ALL be true" vs. "any ONE is enough" before choosing between `and` and `or` — don't rely on how the English sentence happens to sound.*

---

## 6. Summary

- **`if`/`elif`/`else`** checks conditions top to bottom and runs only the first block whose condition is `True` — later branches are never even checked once a match is found.
- **Indentation is Python's block structure** — there's no `{ }`; inconsistent indentation is a `SyntaxError`, not a style nitpick.
- **Nested conditionals** put one decision inside another for genuinely layered logic, but nesting past two or three levels is usually a sign to switch to `and`/`or`.
- **`and` requires every condition to hold; `or` needs just one.** Swapping one for the other is a common, easy-to-miss bug because both still read like plausible English.
- **The ternary expression** (`value_if_true if condition else value_if_false`) compresses a simple two-outcome decision into one line — and should stay limited to exactly that.

Up next: loops — how Python repeats a block of code instead of writing it out again and again.

---

*© 2026 Revature · AI Native Engineering — Foundations · Unit 2.1 · Version 1.0*

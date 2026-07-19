# Operators & Expressions

---

## 1. Learning Objectives

By the end of this unit, you will be able to:

✓ Use all seven arithmetic operators correctly, including the difference between `/` and `//`.  
✓ Predict the result of a multi-operator expression using Python's precedence rules.  
✓ Use comparison operators to get a `True`/`False` answer out of two values.  
✓ Combine conditions with `and`, `or`, and `not`, and explain why Python sometimes skips checking part of an expression.  
✓ Say which non-boolean values Python treats as `True` or `False` automatically.

---

## 2. Overview

A variable, which you met in the last unit, is a noun — it just holds a value. An **operator** is the verb: a symbol that tells Python to *do* something with one or more values, like add them, compare them, or check a condition. Put a few values and operators together and you get an **expression** — a little sentence Python can evaluate down to a single result.

This unit is about learning that vocabulary of verbs, and — just as importantly — the grammar rules for which verb "wins" when a sentence has more than one. Get the grammar wrong and Python won't complain; it will just quietly give you a different, wrong answer using the exact same symbols. That's the trap this unit is built to help you avoid.

---

## 3. Description

### 3.1 Arithmetic Operators

These do calculations on numbers. Each side of an operator is called an **operand** — in `5 + 2`, the operands are `5` and `2`.

| Operator | Meaning | Example | Result |
|---|---|---|---|
| `+` | Addition | `5 + 2` | `7` |
| `-` | Subtraction | `5 - 2` | `3` |
| `*` | Multiplication | `5 * 2` | `10` |
| `/` | True division | `5 / 2` | `2.5` |
| `//` | Floor division | `5 // 2` | `2` |
| `%` | Modulo | `5 % 2` | `1` |
| `**` | Exponent (power) | `5 ** 2` | `25` |

Two of these need a second look, because they're the ones that trip people up:

- **`/` vs `//`.** `/` gives you the exact decimal answer. `//` ("floor division") throws away everything after the decimal point and keeps only the whole number — it *floors* the result down.
- **`%` (modulo)** gives you the *leftover* after dividing as far as whole numbers go. It's how Python checks "is this number even?" — `n % 2` is `0` for even numbers and `1` for odd ones, because there's nothing (or exactly one) left over.

```python
print(7 / 2)     # exact answer
print(7 // 2)    # whole-number part only
print(7 % 2)     # what's left over
```

Output:

```
3.5
3
1
```

### 3.2 Operator Precedence

When an expression has more than one operator, Python doesn't necessarily work left to right — it works in a fixed **priority order**, called precedence.

Think of it like a hospital triage desk. Patients aren't treated strictly in the order they walked in; the more urgent case gets seen first, regardless of arrival order. Python's operators work the same way: some operators have higher "priority" and get evaluated first, no matter where they sit in the line you typed. The order, highest priority first: **parentheses**, then **exponent (`**`)**, then **multiply / divide / floor-divide / modulo**, then **add / subtract**.

```python
result = 2 + 3 * 4
print(result)
```

Output:

```
14
```

Here, `3 * 4` gets treated first — not because it comes second in the line, but because multiplication outranks addition — giving `2 + 12`. If you actually wanted `(2 + 3) * 4`, you have to say so with parentheses:

```python
result = (2 + 3) * 4
print(result)
```

Output:

```
20
```

Parentheses always jump the queue, ahead of every other operator — which makes them the one tool you can always reach for when you're not 100% sure what order Python will pick, or you just want the code to be unmistakable to the next person reading it.

### 3.3 Comparison Operators

These compare two operands and always hand back a `bool` — Python's `True`/`False` type.

| Operator | Meaning | Example | Result |
|---|---|---|---|
| `==` | Equal to | `5 == 5` | `True` |
| `!=` | Not equal to | `5 != 3` | `True` |
| `<` | Less than | `3 < 5` | `True` |
| `>` | Greater than | `3 > 5` | `False` |
| `<=` | Less than or equal to | `5 <= 5` | `True` |
| `>=` | Greater than or equal to | `4 >= 5` | `False` |

```python
temperature = 39.5
print(temperature > 38)
```

Output:

```
True
```

One habit to build immediately: `==` *compares* two values; a single `=` *assigns* a value. They look almost identical and do completely different jobs — mixing them up is one of the most common early mistakes in any language that uses this style of syntax.

**Chaining comparisons.** Python also lets you write a range check the way you'd write it on paper. Instead of `age >= 18 and age < 65`, you can write it as one chain:

```python
age = 30
print(18 <= age < 65)
```

Output:

```
True
```

Python checks both sides for you and combines them — read `18 <= age < 65` as "is `age` at least 18, and also under 65?" in a single breath, without needing `and` at all.

### 3.4 Logical Operators and Short-Circuit Evaluation

Sometimes one condition isn't enough — you need to combine several. That's what `and`, `or`, and `not` do, and the cleanest way to think about them is a bouncer checking a list of entry rules:

- **`and`** — every rule must pass. Fail even one, and the whole thing is `False`. (ID *and* correct dress code — miss either, you're not getting in.)
- **`or`** — just one rule passing is enough for the whole thing to be `True`. (Guest list *or* VIP pass — either one works.)
- **`not`** — flips a `True`/`False` result to its opposite.

```python
has_ticket = True
has_valid_id = False

entry_allowed = has_ticket and has_valid_id
print(entry_allowed)
```

Output:

```
False
```

**Short-circuit evaluation:** Python stops checking an expression the instant it already knows the final answer, without looking at what's left. With `and`, the moment the left side is `False`, Python already knows the whole expression is `False` and never even looks at the right side. With `or`, the moment the left side is `True`, it already knows the whole expression is `True` and skips the right side too.

That's exactly what a real bouncer does: the moment he sees you have no ticket, he doesn't bother checking your ID at all — the answer's already "no."

### 3.5 Truthiness — When Non-Boolean Values Act Like `True` or `False`

`True` and `False` (capital first letter — these are the only two `bool` values) aren't the only things Python will accept where a `True`/`False` answer is expected. Certain values are automatically treated as one or the other, even with no comparison in sight: `0`, `0.0`, and `""` (empty string) all act **falsy**; any non-zero number or non-empty string acts **truthy**.

```python
cart_items = ""

if cart_items:
    print("Proceed to checkout")
else:
    print("Your cart is empty")
```

Output:

```
Your cart is empty
```

Notice there's no `== ""` anywhere — the empty string is simply *falsy* on its own, so the bouncer at that `if` gate turns it away automatically.

---

## 4. Real-World Application

A ride-hailing app's fare estimate is §3.1 and §3.2 in action: base fare, distance, and a surge multiplier get combined with `+` and `*`, and precedence — not the order you'd read it in — decides the multiplier is applied before the totals are added. An exam portal flashing "Pass" the instant you submit is §3.3: one comparison, `marks >= 40`, evaluated straight to a `bool`.

A payment app blocking a UPI transfer runs on §3.4's `and`: sufficient balance **and** a correct PIN, both required — and short-circuit evaluation means if the balance check already fails, the PIN may never even get checked. A checkout button greyed out on an empty cart is §3.5's truthiness at work — the app checks the cart directly, letting an empty list or string act as automatically falsy instead of writing out `if len(cart) != 0`.

---

## 5. Worked Example

**Goal:** Build a small scholarship-eligibility check, watch it give a wrong-looking answer, and find out *why* using precedence and truthiness — not just get a working script.

**1. Set the numbers.**

```python
gpa = 8.2
attendance = 68
backlogs = 0
```

**2. Write a first attempt at the condition.**

```python
eligible = gpa >= 8 and attendance >= 75 or backlogs == 0
print("Scholarship eligible:", eligible)
```

Output:

```
Scholarship eligible: True
```

**3. This looks wrong — attendance is only 68, below the 75 cutoff.** The bug isn't in the values; it's in precedence. Python evaluates `and` before `or`, so this line actually reads as `(gpa >= 8 and attendance >= 75) or (backlogs == 0)` — and since `backlogs == 0` is `True` on its own, the whole thing becomes `True` regardless of attendance.

**4. Fix it with parentheses that say what you actually meant.**

```python
eligible = gpa >= 8 and (attendance >= 75 or backlogs == 0)
print("Scholarship eligible:", eligible)
```

Output:

```
Scholarship eligible: False
```

**5. Confirm it responds correctly if attendance improves.**

```python
attendance = 80
eligible = gpa >= 8 and (attendance >= 75 or backlogs == 0)
print("Scholarship eligible:", eligible)
```

Output:

```
Scholarship eligible: True
```

*Common mistake this exercise is built around: assuming that because every value was correct, the result must be correct too. Precedence can silently regroup a condition into something you never intended — when a boolean expression's answer looks suspicious, check the grouping before you check the numbers.*

---

## 6. Summary

- An **operator** acts on values (**operands**) to produce a result; a chain of them is an **expression**.
- **`/` always gives a decimal; `//` keeps only the whole number; `%` gives you the leftover** — the three easiest arithmetic operators to mix up.
- **Precedence is a priority order, not a left-to-right reading** — like triage, not a queue — and parentheses always jump to the front of that order.
- **Comparison operators** (`==`, `!=`, `<`, `>`, `<=`, `>=`) always produce a `bool`; never confuse `==` (compare) with `=` (assign).
- **`and`/`or`/`not`** combine conditions like a bouncer's checklist, and Python's short-circuit evaluation skips a check once the answer is already certain.
- **Truthiness** lets values like `0` or `""` act as automatic `False` in a condition, with no explicit comparison needed.

Next up: statements, type conversion, and formatted output — how to take the values and conditions from this unit and actually shape them into readable output.

---

*© 2026 Revature · AI Native Engineering — Foundations · Unit 1.3 · Version 1.0*

# Statements, Conversion & Output

---

[← Previous: 1.3 Operators & Expressions](unit-1-3-operators-expressions.md) | [Go back to TOC](../../README.md) | [Next: 2.1 Conditionals →](../p2-control-structures-functions-tooling/unit-2-1-conditionals.md)

# 1.4 Statements, Conversion & Output

---

## What is a Statement?

A **statement** is one complete instruction Python executes. Everything you've written so far — `x = 5`, `print(x)`, `total = price * qty` — is a statement.

This unit is about turning "I computed something" into "I displayed it correctly" — converting values, formatting output, and keeping code readable.

Open a new cell in your Colab notebook and try each example as you go.

**Quick glossary:**

| Term | Meaning |
|---|---|
| **Statement** | One instruction Python executes |
| **Type conversion / casting** | Turning a value into a different type |
| **Truncation** | Chopping off decimals when converting to `int` — no rounding |
| **f-string** | A string with `{}` that inserts a value directly |
| **Type hint** | A note on expected type — not enforced by Python |
| **`match`/`case`** | Compares a value against a list of options, top to bottom |

---

## Assignment vs Expression Statements

- **Assignment statement** — stores a value: `x = 5`
- **Expression statement** — runs and produces a result/side effect: `print(x)`

Two shortcuts worth knowing:

```python
a = b = 5          # chained assignment — both a and b become 5
x, y = 1, 2        # tuple unpacking — x=1, y=2

x, y = y, x        # swap — no temporary variable needed
```

Python always evaluates the right-hand side fully first — that's exactly what makes the swap work.

**One thing to watch:** the number of names and values must match, or Python raises an error.

```python
x, y = 1, 2, 3   # ValueError — 2 names, 3 values
```

---

## Type Conversion

Real data almost always arrives as text — a price from a form, an age typed by a user. You have to convert it before doing math with it.

**Python does a little conversion automatically** — but only between compatible number types:

```python
print(3 + 4.0)   # 7.0 — int promotes to float, no work needed from you
```

It will never do this between `str` and a number on its own — that always needs an explicit function:

| Function | Converts to | Example | Result |
|---|---|---|---|
| `str(x)` | text | `str(42)` | `"42"` |
| `int(x)` | whole number | `int("5")` | `5` |
| `float(x)` | decimal number | `float("3.14")` | `3.14` |
| `bool(x)` | true/false | `bool(0)` | `False` |

**Watch out for two things:**

- `int()` **truncates**, it doesn't round — `int(3.9)` is `3`, not `4`. And `int(-3.9)` is `-3`, always moving toward zero.
- `int("3.14")` raises a `ValueError` — the string must look like a *whole* number. Use `float()` first if there's a decimal point.

```python
raw_quantity = "3"
quantity = int(raw_quantity)
print(quantity)
print(type(quantity))
```
```
3
<class 'int'>
```

**Strings and numbers don't mix without conversion:**

```python
"5" + 3     # TypeError — can't add text and a number
int("5") + 3   # 8 — works once converted
```

---

## f-strings — Clean Output

An f-string embeds a value directly in the text using `{}`:

```python
item = "Samosa"
price = 15.0
print(f"{item} costs Rs.{price:.2f} each")
```
```
Samosa costs Rs.15.00 each
```

**Forgetting the `f`** prints the literal text `{price}` instead of the value — a very easy typo to miss.

**Common format specifiers:**

| Specifier | Does what | Example | Result |
|---|---|---|---|
| `:.2f` | 2 decimal places (money) | `f"{15:.2f}"` | `15.00` |
| `:d` | plain integer | `f"{7:d}"` | `7` |
| `:>10` | right-align, 10 wide | `f"[{'Tea':>10}]"` | `[       Tea]` |
| `:^15` | center, 15 wide | `f"[{'Tea':^15}]"` | `[      Tea       ]` |

f-strings are the modern, recommended way to format output — cleaner than `.format()` or the older `%` style:

| Style | Example | Notes |
|---|---|---|
| f-string | `f"{name}"` | Cleanest — variable sits right where it's used |
| `.format()` | `"{}".format(name)` | Still works, seen in older code |
| `%` | `"%s" % name` | Oldest style, easy to mismatch — avoid in new code |

---

## Type Hints

A type hint documents what type a variable is *meant* to hold:

```python
count: int = 0
item: str = "Samosa"
```

**Important:** Python does **not enforce** this. `age: int = "twenty"` runs without any error — the hint is just documentation for humans and editors, not a guarantee.

---

## `match` / `case`

Use this when a value can be one of a fixed set of options — cleaner than a long `if`/`elif` chain:

```python
order_status = "PREPARING"

match order_status:
    case "PLACED":
        print("Order received.")
    case "PREPARING":
        print("Your order is being prepared.")
    case "READY":
        print("Order ready for pickup!")
    case _:
        print("Status unavailable.")
```
```
Your order is being prepared.
```

Python checks each `case` top to bottom and stops at the first match. `case _:` is the wildcard — it catches anything else, so always put it **last**.

---

## Comments

```python
# this line is ignored by Python
```

Write comments that explain **why**, not what — the code already shows what it does. And keep spacing/style consistent (PEP 8) so teammates can read it easily.

---

## Try it Yourself

A canteen receipt — item, price (as text, like it would arrive from an order form), and quantity:

```python
item: str = "Samosa"
price_text: str = "15.0"
quantity: int = 3

price = float(price_text)
total = price * quantity

print(f"{item:>10}: {quantity:d} x Rs.{price:.2f} = Rs.{total:.2f}")
```
```
    Samosa: 3 x Rs.15.00 = Rs.45.00
```

**Your turn:** add a second item, `"Coffee"`, priced at `"35.0"` for `2` cups. Print the same style of receipt line.

Then add a `match`/`case` for `payment_mode`, which can be `"CASH"`, `"UPI"`, or `"CARD"` — print a different confirmation for each, plus a wildcard for anything else.

---

## Common Mistakes

- Assuming text that "looks like a number" already behaves like one — it's `str` until you convert it
- Using `int()` on a decimal-looking string — `int("3.14")` raises `ValueError`; convert with `float()` first
- Expecting `int()` to round — it truncates toward zero instead
- Forgetting the `f` prefix on an f-string
- Believing a type hint is enforced — it isn't
- Putting `case _:` before other cases — nothing after it will ever run

---

## Interview Questions

**Q: What's the difference between implicit and explicit type conversion?**
A: Python does a small amount automatically — `3 + 4.0` becomes `7.0` because `int` promotes to `float`. But it never converts `str` to a number on its own — that always needs `int()`, `float()`, or similar, explicitly.

**Q: Why does `int("3.14")` fail but `int(3.14)` work?**
A: `int()` on a string tries to parse whole-number digits only — a decimal point breaks that. `int()` on an actual float truncates it instead. Two different code paths inside the same function.

**Q: What's `match`/`case` used for?**
A: Comparing one value against a fixed set of options, running the first match, cleaner than a long `if`/`elif` chain. Introduced in Python 3.10.

---

## Quick Recap

- A statement is one instruction; assignment stores, expressions run/produce a result.
- Convert text to numbers explicitly with `int()`/`float()` before doing math with them.
- `int()` truncates, never rounds; `int("3.14")` raises `ValueError`.
- f-strings (`f"{value:.2f}"`) are the modern way to format output.
- Type hints document intent but aren't enforced.
- `match`/`case` picks the first matching option; keep `case _:` last.
- Comments explain *why*, not *what*.

## Reference Links

- [Python 3 Documentation — Built-in Functions (`int`, `float`, `str`, `bool`)](https://docs.python.org/3/library/functions.html)
- [Python 3 Documentation — Formatted String Literals (f-strings)](https://docs.python.org/3/reference/lexical_analysis.html#f-strings)
- [PEP 498 — Literal String Interpolation](https://peps.python.org/pep-0498/)
- [PEP 634 — Structural Pattern Matching: Specification](https://peps.python.org/pep-0634/)
- [PEP 8 — Style Guide for Python Code](https://peps.python.org/pep-0008/)
- [Real Python — Python's F-String for String Interpolation and Formatting](https://realpython.com/python-f-strings/)
- [W3Schools — Python Casting (Type Conversion)](https://www.w3schools.com/python/python_casting.asp)

[← Previous: 1.3 Operators & Expressions](unit-1-3-operators-expressions.md) | [Go back to TOC](../../README.md) | [Next up: **Unit 2.1 — Conditionals** — where your programs make their first real decisions with `if`, `elif`, and `else`. →](../p2-control-structures-functions-tooling/unit-2-1-conditionals.md)

---

*© 2026 Revature · AI Native Engineering — Foundations · Unit 1.4 · Version 2.0*

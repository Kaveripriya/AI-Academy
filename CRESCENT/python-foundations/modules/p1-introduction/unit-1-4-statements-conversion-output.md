# Statements, Conversion & Output

---

## 1. Learning Objectives

By the end of this unit, you will be able to:

✓ Tell a statement apart from an expression, and write chained assignment and tuple unpacking correctly.  
✓ Convert values between `int`, `float`, `str`, and `bool`, and explain why float-to-int truncates instead of rounding.  
✓ Build formatted output with f-strings and format specifiers.  
✓ Recognize what a type hint and a basic `match-case` statement look like, and why comments and PEP 8 matter.

---

## 2. Overview

You already know how to store a value (unit 1.2) and combine values with operators (unit 1.3). This unit covers the last three basics before Part 1 wraps up.

First, what actually counts as one complete instruction to Python, versus just a value sitting there (a **statement**). Second, how to convert a value from one type to another when you need to — turning the text `"25"` you get from a form into the number `25` you can do math with, without changing what quantity it actually represents (**type conversion**). Third, how to control exactly how a value looks when you show it to someone — because the raw way Python stores a number is rarely how you'd want to display it (**formatted output**). You'll also get a quick first look at two tools you'll use more seriously later: type hints and `match-case`.

Here's a real-world parallel for the second and third ideas, now that you know what they mean: 37°C and 98.6°F are the same temperature — converting between them doesn't change how hot it actually is, only how that heat gets written down. That's type conversion. Formatting works the same way once you're already in the right type: a price stored internally as `8.7` and a price *displayed* as `₹8.70` are the same amount — formatting just decides how many digits show up and where the symbol goes.

Every later topic in this program comes back to the same pattern this unit builds: get a value, make sure it's the right type, then show it properly. Reading a file, checking what a user typed into a form, displaying a model's prediction on a screen — all of it is this same toolbox at work.

---

## 3. Description

### 3.1 Statements vs. Expressions

An **expression** is anything that produces a value — `2 + 3` is an expression; it evaluates to `5`. A **statement** is a complete instruction — it does something, and an assignment is the most common example: `age = 21` uses the expression `21` but also performs an action (storing it). Every line of runnable Python code is a statement; not every statement contains something worth calling an expression on its own, and not every expression is a statement (typing `2 + 3` alone at the REPL evaluates it but doesn't store anything).

**Chained assignment** puts the same value into several variables in a single line — useful when multiple things genuinely start out equal:

```python
a = b = 5
print(a, b)
```

Output:

```
5 5
```

**Tuple unpacking** assigns several different values to several variables in one line, matched left to right in order:

```python
x, y = 1, 2
print(x, y)
```

Output:

```
1 2
```

It also gives you Python's classic one-line variable swap, with no temporary variable needed:

```python
x, y = y, x
print(x, y)
```

Output:

```
2 1
```

### 3.2 Type Conversion — Same Value, Different Container

**Type conversion** changes a value's type without changing the real quantity it represents — the underlying value survives, only its container changes. It's the same idea as converting inches to centimeters: `30 cm` and `11.8 inches` describe the exact same physical length; nothing about the object changed, only the unit used to write it down.

This matters immediately because of one fact worth memorizing now: **anything typed into a form, read from a keyboard, or read from a file arrives as a `str` — always**, even if it visually looks like a number. Python will not silently guess that `"21"` means the number 21; you must convert it yourself.

| Function | Converts to | Example |
|---|---|---|
| `int()` | Whole number | `int("21")` → `21` |
| `float()` | Decimal number | `float("21")` → `21.0` |
| `str()` | Text | `str(21)` → `"21"` |
| `bool()` | True/False | `bool(0)` → `False` |

```python
age_text = "21"
age_number = int(age_text)
print(age_number + 1)
```

Output:

```
22
```

One conversion behaves in a way beginners consistently get wrong: converting a `float` to an `int` does **not** round to the nearest whole number — it **truncates**, meaning it simply chops off everything after the decimal point, regardless of which way the number was leaning.

```python
print(int(9.9))
print(int(-9.9))
```

Output:

```
9
-9
```

`int(9.9)` becomes `9`, not `10` — Python cut the decimal off rather than rounding up. If you actually want rounding, that's a different tool (`round()`), not `int()`.

### 3.3 String Basics

A **string** (`str`) is text, always written inside quotes — single or double, it makes no difference to Python: `'hello'` and `"hello"` are identical. Double quotes are handy when the text itself contains an apostrophe, like `"it's fine"`, so you don't have to escape it.

**Concatenation** joins strings together with `+`:

```python
first_name = "Arjun"
last_name = "Mehta"
full_name = first_name + " " + last_name
print(full_name)
```

Output:

```
Arjun Mehta
```

Every character in a string sits at a numbered position, called an **index**, starting at `0` — not `1`:

```python
word = "Python"
print(word[0])
print(word[1])
```

Output:

```
P
y
```

You'll use indexing constantly once you reach lists in Part 3 — strings are your first exposure to the idea that "position 0 is the first item," which trips up almost everyone exactly once.

### 3.4 Formatted Output with f-strings

An **f-string** embeds a variable or expression directly inside a piece of text, by putting an `f` right before the opening quote — it's how you build formatted output. This is the same "stored value vs. displayed value" idea from the Overview: a value tracked internally as `78.456000001` can still be shown to someone as `78.46`, and an f-string is the tool that controls that:

```python
name = "Priya"
marks = 78.456
print(f"Student: {name}, Marks: {marks}")
```

Output:

```
Student: Priya, Marks: 78.456
```

That printed the raw value. To control *how* it's displayed, add a **format specifier** — extra formatting instructions after a colon `:` inside the curly braces:

| Specifier | Meaning | Example | Result |
|---|---|---|---|
| `:.2f` | Round to 2 decimal places | `f"{marks:.2f}"` | `78.46` |
| `:d` | Display as a whole number | `f"{25:d}"` | `25` |
| `:>10` | Right-align within 10 characters | `f"{'hi':>10}"` | `        hi` |
| `:^15` | Center-align within 15 characters | `f"{'hi':^15}"` | `      hi       ` |
| `:,` | Insert thousands separators | `f"{1000000:,}"` | `1,000,000` |

```python
print(f"Marks: {marks:.2f}")
```

Output:

```
Marks: 78.46
```

### 3.5 A First Look at Type Hints and `match-case`

A **type hint** is a note attached to a variable or function that says what type of value belongs there — it doesn't physically stop you from putting in something else, but it tells the next person (or a tool checking your code) what was intended. Python never enforces it at runtime; think of it as documentation with structure, not a rule. You'll see hints on plain variables, and on function parameters and return values:

```python
count: int = 0

def celsius_to_fahrenheit(c: float) -> float:
    return (c * 9 / 5) + 32
```

`match-case` compares an incoming value against a list of known categories, one by one, and routes it to whichever one matches — like a sorting station where each item gets sent to the right bin.

```python
status = "Task"

match status:
    case "Task":
        print("This is a task")
    case "Bug":
        print("This is a bug")
    case _:
        print("Unknown category")
```

Output:

```
This is a task
```

`case _:` matches anything that didn't fit an earlier category — the catch-all bin at the end of the sorting station. Both of these get much more use later in the program; for now, just recognize the shape when you see it.

### 3.6 Comments and PEP 8

A **comment** starts with `#`. Python skips it entirely when running your code — it exists purely so a human reading the file later (often you, in three weeks) understands *why* a line exists, not just what it does.

```python
# Convert form input to a number before doing math on it
age = int(age_text)
```

**PEP 8** is Python's official style guide — a shared set of conventions (clear names, consistent spacing, `snake_case` for variables, which you met in Unit 1.2) that most Python code in the world follows. Nothing forces you to follow it, but code that doesn't tends to be much harder for anyone else — including future you — to read.

---

## 4. Real-World Application

A food delivery app's final bill always shows exactly two decimals — `247.699999` internally, `₹247.70` on your screen — because it ran through an f-string with `:.2f` (§3.4) before ever reaching the display. The same idea shows up any time you see `12,438 steps` instead of `12438`: that's the `:,` specifier inserting thousands separators purely for readability, not a different underlying number.

Conversion shows up just as constantly on the input side. A signup form checking that you're old enough only works because your typed age arrived as a `str` and got passed through `int()` (§3.2) before anything compared it to a minimum age — skip that step, and the form would be comparing text to a number and failing outright, exactly like §5's broken Cell 4.

---

## 5. Worked Example

**Goal:** Build a small ticket-price calculator for a college fest — take a price the way it would actually arrive from a web form (as text), convert it, apply a discount, and print a properly formatted receipt line.

**Cell 1 — simulate the form input.** Form data always arrives as text, so start there on purpose:

```python
price_text = "499"
```

**Cell 2 — convert it to something you can do math on.**

```python
price = float(price_text)
print(price)
```

Output:

```
499.0
```

**Cell 3 — apply a 10% student discount and format the receipt line.**

```python
final_price = price * 0.9
print(f"Final ticket price: Rs. {final_price:.2f}")
```

Output:

```
Final ticket price: Rs. 449.10
```

**Cell 4 — now break it on purpose.** Skip the conversion step and try the same math directly on the text:

```python
price_text = "499"
final_price = price_text * 0.9
```

Output:

```
TypeError: can't multiply sequence by non-int of type 'float'
```

Python isn't confused about what you meant — it's refusing to guess. `price_text` is still a `str`, and Python will not silently treat text as a number just because it looks like one. The fix isn't different math; it's remembering to convert first, exactly like Cell 2 did.

*Common mistake: assuming a value that "looks like a number" already behaves like one. Anything from a form, a file, or user input is text until you convert it yourself — every single time.*

---

## 6. Summary

- A **statement** is a complete instruction Python carries out; chained assignment and tuple unpacking both let you write more than one assignment on a single line.
- **Type conversion** (`int()`, `float()`, `str()`, `bool()`) changes a value's container, not the value itself — and anything from a form, file, or keyboard starts out as `str`, full stop.
- **Float-to-int truncates, it doesn't round** — `int(9.9)` is `9`, not `10`.
- **f-strings** embed values directly in text, and format specifiers like `:.2f` or `:,` control exactly how those values are displayed.
- **Type hints** are unenforced notes about expected types; **`match-case`** routes a value to the branch matching its pattern — both are worth recognizing now, even before you use them heavily.

That's the end of Part 1. Part 2 moves from single instructions into control structures — starting with conditionals, where your programs make their first real decisions.

---

*© 2026 Revature · AI Native Engineering — Foundations · Unit 1.4 · Version 1.0*

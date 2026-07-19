# Variables, Identifiers & Types

---

## 1. Learning Objectives

By the end of this unit, you will be able to:

✓ Store a value in a variable using `=`, and explain what that line actually does.  
✓ Name a variable correctly, following Python's rules and the snake_case convention.  
✓ Recognize Python's four basic types — `int`, `float`, `str`, `bool` — and pick the right one for a value.  
✓ Use `type()` to check what a variable is holding, and explain what "dynamic typing" means.

---

## 2. Overview

In unit 1.1, every value you printed was typed directly inside `print(...)`. That's fine for one line, but real programs need to hold on to a value, use it several times, and change it later — a total, a name, an answer that gets recalculated. Python's answer to "how do I hold on to this?" is the **variable**.

Think of a variable like a luggage tag at an airport. The tag itself (the **variable name**) has no weight and carries nothing — it's just a label. What matters is the suitcase it's attached to (the **value**). You can read the tag to find the right suitcase, you can peel the tag off and put it on a *different* suitcase later, and — this will matter later in the unit — nothing stops you from tagging a backpack instead of a suitcase next time. The tag doesn't care what it's stuck to.

This unit builds in three steps: how to actually attach a tag to a value (**assignment**), the rules Python enforces on what you're allowed to write on the tag (**identifiers**), and the different *kinds* of things a tag can point to (**types**). By the end, you'll also know how to check, at any moment, exactly what a variable is currently holding.

---

## 3. Description

### 3.1 Variables and Assignment

A **variable** is a name attached to a value so you can reuse that value later without retyping it. You create one with `=`, called the **assignment operator**:

```python
roll_number = 101
```

Python runs this line in two steps: first it works out the value on the right (`101`), then it attaches the name on the left (`roll_number`) to that value. So this line means "let `roll_number` refer to `101`" — it does **not** mean "roll_number is equal to 101," even though `=` is the equals sign you know from math class.

Python has a separate operator for checking whether two things are equal: `==` (two equals signs). You'll use it starting in the next unit. For now, remember the rule: one `=` always means "store this value," never "check if these are the same."

Assigning one variable to another, like `second = first`, copies whatever value `first` currently holds into `second` — it does **not** permanently link the two names together. If `first` changes afterward, `second` doesn't follow along:

```python
first = 5
second = first
first = 99
print(second)
```

Output:

```
5
```

Once a variable holds a value, you can print it, reuse it, or **reassign** it — attach the same tag to a new value, which quietly discards the old one:

```python
roll_number = 101
print(roll_number)

roll_number = 102      # the tag moves; 101 is gone
print(roll_number)
```

Output:

```
101
102
```

### 3.2 Identifiers — Naming Rules and Conventions

The name you choose for a variable is called an **identifier**. Python won't let you write just anything on the tag — there are rules:

- **Can't start with a digit.** Letters or an underscore (`_`) only as the first character. `marks1` is fine; `1marks` is not — Python would have no way to tell where a number ends and a name begins.
- **Letters, digits, and underscores only** after that first character. No spaces, no `-`, no `@`.
- **Case matters.** `Marks`, `marks`, and `MARKS` are three completely different variables to Python — it does not treat them as the same name.
- **Can't be a reserved keyword.** Words like `if`, `for`, `class`, and `return` already mean something specific to Python and can't be reused as a variable name.
- **snake_case is the convention**, not a rule Python enforces but one every Python developer expects: multi-word names are lowercase, joined with underscores — `total_marks`, not `TotalMarks` or `totalMarks`.

```python
student_name = "Arjun"    # valid, and follows convention
```

```python
1st_semester = 78
```

Output:

```
SyntaxError: invalid decimal literal
```

That error happens *before* your program even runs — Python checks names like this immediately, the same way a form rejects an invalid field before you can submit it.

### 3.3 Values and Types

Every value in Python has a **type** — it tells Python what *kind* of thing the value is, which then determines what you're allowed to do with it. You can add two numbers, but adding a number to a name doesn't make sense — the type is what lets Python catch that difference.

Four types cover almost everything you'll write for now:

**`int` — a whole number, no decimal point.** `101`, `-5`, `0`.

**`float` — a number with a decimal point.** `8.7`, `-0.5` — even `5.0` counts as a `float`, decimal point or not.

**`str` — text, wrapped in quotes.** `"Arjun"`, `'Chennai'` — single or double quotes both work, as long as they match.

**`bool` — a switch with exactly two settings.** Only ever `True` or `False`, nothing in between.

*Why bother knowing this if you never write the type anywhere? Because Python enforces it behind the scenes even when you don't see it. `"5" + "3"` gives you `"53"` — joining two pieces of text — while `5 + 3` gives you `8` — adding two numbers — using the exact same `+` symbol. The type is what decides which one you get, and the next unit shows you exactly how.*

```python
roll_number = 101        # int   — a whole number
cgpa = 8.7                # float — has a decimal point
student_name = "Arjun"    # str   — text, in quotes
is_passed = True          # bool  — True or False only
```

Notice you never wrote the type anywhere — you just wrote the value, and Python figured out the type from *how* you wrote it (quotes mean text, a decimal point means `float`, and so on).

### 3.4 Inspecting Types and Dynamic Typing

If you're ever unsure what a variable is currently holding, ask Python directly with `type()`:

```python
print(type(roll_number))
print(type(cgpa))
print(type(student_name))
print(type(is_passed))
```

Output:

```
<class 'int'>
<class 'float'>
<class 'str'>
<class 'bool'>
```

Python never makes you declare in advance what type a variable will hold. It just decides the type from whatever value the variable currently holds — and the *same* variable name can be reattached to a completely different type later. This is called **dynamic typing**.

The luggage-tag idea earns its keep here: many other languages make you declare up front what type a variable will *always* hold — the equivalent of writing "this tag can only ever go on suitcases." Python's tag doesn't care what it's stuck to, which is exactly the flexibility (and the risk) dynamic typing gives you:

```python
data = 10          # right now, an int
print(type(data))

data = "ten"       # same name, now a str
print(type(data))
```

Output:

```
<class 'int'>
<class 'str'>
```

This is convenient, but it's also a real source of bugs if you're not paying attention: an operation that works fine on an `int` can fail on a `str`, and Python will only tell you *when it gets there* — not before, the same "errors surface only when that line runs" behavior from unit 1.1.

---

## 4. Real-World Application

A UPI app's home screen is a good place to see this without any code in front of you. Your balance is a `float` — it has decimal paise — while the transaction status underneath it ("Success", "Failed", "Pending") is a `str`. Two different types, doing two different jobs, and both are just variables somewhere in that app's code, exactly like `cgpa` and `student_name` above.

The Instagram follower count you watch tick upward is the **reassignment** idea from §3.1 running live: it's a single `int` variable, and every new follower just reassigns it to one number higher. You're not seeing a new value get created — you're seeing the same variable get told to hold something different, over and over.

---

## 5. Worked Example

**Goal:** Store a small student record, inspect its types, then deliberately trigger and fix a naming error.

**1. Create the record.**

```python
roll_number = 101
student_name = "Arjun"
cgpa = 8.7
is_passed = True
```

**2. Print each value next to its type.**

```python
print(roll_number, type(roll_number))
print(student_name, type(student_name))
print(cgpa, type(cgpa))
print(is_passed, type(is_passed))
```

Output:

```
101 <class 'int'>
Arjun <class 'str'>
8.7 <class 'float'>
True <class 'bool'>
```

**3. Try to name a variable after this semester, starting with the number.**

```python
1st_sem_cgpa = 8.7
```

Output:

```
SyntaxError: invalid decimal literal
```

**4. Rename it so the number isn't first, and rerun.**

```python
sem_1_cgpa = 8.7
print(sem_1_cgpa)
```

Output:

```
8.7
```

**5. Now reassign `cgpa` to a `str` instead of a `float`, and check the type again.**

```python
cgpa = "8.7 (provisional)"
print(type(cgpa))
```

Output:

```
<class 'str'>
```

Nothing crashed — Python allowed it instantly, because dynamic typing means the variable was never locked to `float` in the first place. That's exactly the flexibility (and the risk) from §3.4.

*Common mistake: assuming a variable will always be the type it started as. If you reassign it somewhere else in a long notebook, a calculation further down that expected a number can fail on what is now text — always check with `type()` if you're not sure.*

---

## 6. Summary

- A **variable** is a name attached to a value with `=` — reading `=` as "attach this name to this value," never as "is equal to."
- An **identifier** must start with a letter or underscore, can't be a reserved keyword, and is case-sensitive; snake_case is the expected style even though Python doesn't force it.
- Python's four basic **types** — `int`, `float`, `str`, `bool` — are worked out automatically from how you write the value, not from any declaration you make.
- **`type()`** tells you exactly what a variable currently holds, and **dynamic typing** means that answer can change if the variable gets reassigned to a different kind of value later.

Next up: operators and expressions — how you combine and compare the values you now know how to store.

---

*© 2026 Revature · AI Native Engineering — Foundations · Unit 1.2 · Version 1.0*

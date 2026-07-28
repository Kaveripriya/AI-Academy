# Variables, Identifiers & Types

---

[← Previous: 1.1 The Python Environment](unit-1-1-python-environment.md) | [Go back to TOC](../../README.md) | [Next: 1.3 Operators & Expressions →](unit-1-3-operators-expressions.md)


---

## What is a Variable?

A **variable** is a name that holds a value, so you can reuse it or change it later — instead of retyping the value everywhere.

You create one with `=`:

```python
x = 5
```

Read this as "`x` now refers to `5`" — not "x equals 5" like in maths. In Python, `=` is an action, not a fact.

Open a new cell in your Colab notebook from Unit 1.1, and try each example below as you go.

**Quick glossary:**

| Term | Meaning |
|---|---|
| **Variable** | A name that refers to a value |
| **Assignment (`=`)** | Binds a name to a value |
| **Reassignment** | Giving an existing variable a new value |
| **Identifier** | The name you choose for a variable |
| **Type** | What kind of value it is — number, text, true/false |
| **Dynamic typing** | Python figures out the type for you, automatically |

---

## Why Variables Matter

Without them, every program can only `print()` fixed text once and forget it. Real apps need to:

- **Remember** a value (a cart total while you keep shopping)
- **Reuse** a value (a tax rate applied to every item)
- **Update** a value (a bank balance after a transaction)

One variable, one name, handles all three.

---

## Reassignment

The same name can point to a new value later — the old one is just gone:

```python
score = 100
print(score)

score = 150
print(score)
```

Output:

```
100
150
```

---

## Naming Rules

- Letters, digits, and `_` only
- Can't start with a digit — `age2` ✅, `2age` ❌
- No spaces or symbols like `-`, `!`, `$`
- Can't use a reserved word — `if`, `for`, `class`, `True`

**Convention (not a rule, just good practice):** use `snake_case` — `total_price`, not `TotalPrice`.

And Python is **case sensitive** — `total` and `Total` are two different variables. This trips people up constantly.

---

## The Four Basic Types

```python
customer_name = "Ananya Roy"   # str   — text, in quotes
delivery_fee  = 29.50          # float — has a decimal point
order_id      = "SWG10234"     # str   — quoted, even though it has digits
is_paid       = False          # bool  — True or False only
```

Check any value's type with `type()`:

```python
print(type(customer_name))
print(type(delivery_fee))
print(type(order_id))
print(type(is_paid))
```

Output:

```
<class 'str'>
<class 'float'>
<class 'str'>
<class 'bool'>
```

---

## Getting Input from the User

`input()` pauses your program, shows a message, and waits for the person to type something and press Enter:

```python
name = input("Enter your name: ")
print("Hello,", name)
```

```
Enter your name: Ada
Hello, Ada
```

**The one thing to remember:** `input()` always returns a **string** — even if the user types a number.

```python
age = input("Enter your age: ")
print(type(age))
```

```
Enter your age: 20
<class 'str'>
```

Try `age + 5` right now and Python throws a `TypeError` — you can't add a number to text. You'd need `int(age)` first (covered in the next unit).

---

## Static vs Dynamic Typing

| | Static (Java, C) | Dynamic (Python) |
|---|---|---|
| Declare type up front? | Yes — `int age = 30;` | No — Python figures it out |
| When is type checked? | Before running | While running |
| Can a variable change type? | No | Yes — same name, different type later |

```python
data = 100
print(type(data))   # int

data = "one hundred"
print(type(data))   # str
```

Same name, different type — because the type belongs to the **value**, not the name.

---

## Try it Yourself

**(a)** Create `restaurant_name = "Spice Route"`, print it, then print its type.

```python
restaurant_name = "Spice Route"
print(restaurant_name)
print(type(restaurant_name))
```
```
Spice Route
<class 'str'>
```

**(b)** Now add `quantity = 3` and `packing_charge = 15.0`. Print all three types.

```python
print(type(restaurant_name))
print(type(quantity))
print(type(packing_charge))
```
```
<class 'str'>
<class 'int'>
<class 'float'>
```

**Your turn:** using `input()`, ask for the customer's name, print `"Order for:"` followed by it. Then create `order_confirmed = False`, reassign it to `True`, and print the result.

---

## Common Mistakes

- Using a variable before assigning it → `NameError: name '...' is not defined`
- Starting a name with a digit (`2age = 30`) → `SyntaxError`
- Naming something after a reserved word (`class = "10A"`) → `SyntaxError`
- Assuming `total` and `Total` are the same variable — they're not
- Confusing `"5"` (a string) with `5` (a number) — check with `type()` if unsure

---

## Interview Questions

**Q: Is Python statically or dynamically typed?**
A: Dynamically typed. You never declare a type up front — Python figures it out from the value, and the same name can hold different types at different points.

**Q: What's the difference between a rule and a convention here?**
A: Identifier rules (no leading digit, no reserved words) are enforced — break them and your code won't run. `snake_case` is a convention — a team agreement for readability, not something Python checks.

**Q: Is `=` the same as `==`?**
A: No. `=` assigns a value. `==` compares two values (covered in the next unit).

---

## Quick Recap

- A variable is a name bound to a value with `=`; reassignment replaces the old value.
- Identifiers follow strict rules; `snake_case` is the convention.
- Python is case sensitive.
- The four basic types: `int`, `float`, `str`, `bool` — check any of them with `type()`.
- `input()` always returns a string, no matter what's typed.
- Python is dynamically typed — the type belongs to the value, not the name.

##  Reference Links

- [The Python Tutorial — An Informal Introduction (Variables, Numbers, Strings)](https://docs.python.org/3/tutorial/introduction.html)
- [Python 3 Documentation — Built-in Types](https://docs.python.org/3/library/stdtypes.html)
- [PEP 8 — Style Guide for Python Code (Naming Conventions)](https://peps.python.org/pep-0008/#naming-conventions)
- [Real Python — Variables in Python](https://realpython.com/python-variables/)
- [W3Schools — Python Variables](https://www.w3schools.com/python/python_variables.asp)

[← Previous: 1.1 The Python Environment](unit-1-1-python-environment.md) | [Go back to TOC](../../README.md) | [Next up: **1.3 Operators & Expressions** — combining and comparing the values you now know how to store. →](unit-1-3-operators-expressions.md)

---

*© 2026 Revature · AI Native Engineering — Foundations · Unit 1.2 · Version 2.0*

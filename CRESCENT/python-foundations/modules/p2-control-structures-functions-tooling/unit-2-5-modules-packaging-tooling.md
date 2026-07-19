# Modules, Packaging & Professional Tooling

---

## 1. Learning Objectives

By the end of this unit, you will be able to:

✓ Import code with `import`, `from ... import`, and `as`, and explain what each one actually does.  
✓ Use at least three modules from Python's standard library.  
✓ Explain what `pip` does, install a package with it, and say what problem a virtual environment solves.  
✓ Describe, at a concept level, what Poetry and Pytest are for and why professional projects use them.

---

## 2. Overview

Every piece of code you've written so far has lived in a single notebook, written entirely by you. Real projects don't work that way: code is split across many files, most of it wasn't written by you at all, and none of it gets trusted until something has actually checked that it works.

This unit covers five tools built for exactly that reality. A **module** is a file of code someone else already wrote that you can pull into your own program. **pip** installs a module you don't have yet. A **virtual environment** keeps one project's installed modules separate from another's, so they can't conflict. **Poetry** writes down exactly which modules and versions a project needs, so anyone can rebuild the same setup. **Pytest** runs small checks that confirm your code still works before you rely on it.

Picture all five together as a workshop. So far, you've been building things using only tools you made yourself, on one workbench. Modules are shelves of tools other people already built; pip is the delivery service that brings in tools you don't have; a virtual environment keeps one project's tools from getting mixed up with another's; Poetry is the labeled inventory list so anyone can rebuild your exact workshop; and Pytest is the inspector who checks your tools still work before you trust them.

Every professional Python project you'll touch later in this course — and after it — is built on exactly this setup.

---

## 3. Description

### 3.1 Modules and Imports

A **module** is just a `.py` file full of code someone already wrote — functions, values, whatever — sitting ready for you to pull into your own program instead of rewriting it from scratch. Bringing one in is called **importing** it.

- **`import`** — brings the whole module in under its own name; you reach into it with a dot. (Like taking the whole toolbox off the shelf.)

```python
import math
print(math.sqrt(81))
```

Output:
```
9.0
```

- **`from ... import`** — pulls one specific item out of the module, so you can use it directly, without the dot. (Like taking a single tool out of the toolbox.)

```python
from math import sqrt
print(sqrt(81))
```

Output:
```
9.0
```

- **`as`** — gives the thing you just imported a shorter name to use from then on. (Like relabeling a toolbox on the way in.)

```python
import math as m
print(m.sqrt(81))
```

Output:
```
9.0
```

Writing `math.sqrt()` instead of just `sqrt()` isn't just habit — it's a label that says *which* toolbox this tool came from. That matters the moment two different modules happen to have a function with the same name; the dot is what keeps them from colliding.

### 3.2 The Python Standard Library

Python's **standard library** is the set of modules that ship with Python itself — nothing to install, nothing to wait for. Picture them as toolboxes that come pre-installed in your workshop the moment you move in.

| **Module** | **What it's for** |
|---|---|
| `random` | Random numbers, shuffling a list, picking a random item. |
| `math` | Square roots, powers, trigonometry, constants like `pi`. |
| `datetime` | Today's date, the current time, the gap between two dates. |

```python
import random
import datetime

print(random.choice(["Rohan", "Priya", "Arjun"]))
print(datetime.date.today())
```

Output:
```
Priya
2026-07-17
```

### 3.3 Installing Packages with `pip`

Beyond what ships pre-installed, there's a much bigger catalog: **PyPI** (the Python Package Index) — a public, shared listing where any developer, anywhere, can publish a toolbox for others to use. `pip` is the delivery service that goes and fetches one for you by name.

```python
!pip install requests
```

Once it arrives, you `import` and use it exactly like anything from the standard library — Python doesn't distinguish between "built-in" and "installed" once the import succeeds. Google Colab happens to keep several popular packages (`requests` included) already stocked, so this particular install is often instant.

### 3.4 Why Virtual Environments Exist

Here's the problem a **virtual environment** solves. Say Project A was built against `requests` version 2.20, and Project B — on the very same laptop — needs `requests` version 2.31 because it uses a feature that didn't exist in 2.20. Without isolation, there's only *one* copy of `requests` installed system-wide: upgrading it for Project B silently breaks Project A, and neither project's code has to change for that to happen.

A virtual environment gives each project its own private room, with its own copy of Python's packages, sealed off from every other project's room — Project A keeps its `requests` v2.20 in its room, Project B keeps v2.31 in its own, and neither room can touch the other's shelves.

On your own laptop, you create one of these rooms per project and step into it (`activate`) before working. Google Colab skips this step for you — every notebook already runs in its own sealed cloud session — but you'll create `venv`s directly the moment you write code outside a notebook.

### 3.5 Poetry — the Project's Inventory List

**Poetry** is a tool that keeps a written, exact record of which packages — and which versions — a project depends on, in one file. Instead of you (or anyone else) trying to remember or guess what to install, running `poetry new my_project` sets up a project with that inventory list already in place. Anyone who clones the project can hand that same list to Poetry and get the *identical* set of tools, with no guesswork.

### 3.6 Pytest — an Inspector for Your Code

**Pytest** is the standard tool for writing automated tests: small pieces of code whose only job is to check that a *different* piece of code still behaves the way it's supposed to, so you're not manually re-checking it by hand after every change.

```python
def add(a, b):
    return a + b

def test_add():
    assert add(2, 3) == 5
```

An `assert` statement is a claim — "this had better be true." Running `pytest` on a file like this automatically finds every function starting with `test_` and checks whether its `assert` lines hold. The moment `add(2, 3)` stops returning `5` — because someone, somewhere, broke it — this test fails immediately and points straight at the problem, instead of a user finding out first.

---

## 4. Real-World Application

A weather app showing today's forecast, and Spotify's shuffle button never repeating the same order twice, both lean on §3.2's standard library doing exactly what you just did by hand — `datetime` picks the right day before a forecast is fetched, `random` reorders the queue — just wired into a much bigger app instead of a two-line script.

Two data science projects on the same laptop needing different, conflicting versions of the same library is precisely the §3.4 problem: solved by giving each project its own virtual environment instead of one shared, global set of packages that both are forced to fight over.

And the two guarantees behind most professional codebases are §3.5 and §3.6 working together: a new developer joining a company project runs one command that reads the project's Poetry inventory and installs exactly what's listed, nothing more or missing — and code doesn't merge into the main project until Pytest's automated checks confirm it didn't break anything that used to work.

---

## 5. Worked Example

**Goal:** Split code across two files instead of one notebook cell, import your own module the same way you'd import `math`, then add a test that checks it.

**1. Write your own module.** In a Colab cell, use `%%writefile` to save code straight to a file instead of running it:

```python
%%writefile grader.py
def letter_grade(score):
    if score >= 90:
        return "A"
    elif score >= 75:
        return "B"
    else:
        return "C"
```

Output:
```
Writing grader.py
```

**2. Import it from a different cell, exactly like a standard library module.**

```python
import grader

print(grader.letter_grade(92))
print(grader.letter_grade(80))
```

Output:
```
A
B
```

Nothing about this `import` is different from `import math` — `grader.py` is a module now, the same category of thing, just one you happened to write yourself two cells ago instead of one the Python team shipped years ago.

**3. Add a test that checks it, the Pytest way.**

```python
def test_letter_grade():
    assert grader.letter_grade(95) == "A"
    assert grader.letter_grade(60) == "C"

test_letter_grade()
print("Test passed — no error means every assertion held.")
```

Output:
```
Test passed — no error means every assertion held.
```

**4. Break it on purpose, and watch the test do its job.** Rewrite `grader.py` so `letter_grade(95)` returns `"B"` by mistake, re-run the import, then re-run `test_letter_grade()`.

Output:
```
AssertionError
```

The test didn't need you to notice the bug by reading the code — it noticed for you, the instant the behavior changed.

*Common mistake: installing a package straight into a shared machine with `pip install` and no virtual environment. On your own laptop, that can quietly upgrade or downgrade a package a completely unrelated project depends on — the exact failure mode §3.4 describes.*

---

## 6. Summary

- **`import`, `from ... import`, and `as`** bring code from a module into your program — a module being nothing more exotic than a `.py` file someone already wrote.
- **The standard library** ships pre-installed with Python; **`pip`** reaches into PyPI's public catalog to fetch anything beyond that, by name.
- **Virtual environments** give each project its own private set of installed packages, so one project's version needs can never silently break another's.
- **Poetry** keeps a written, exact inventory of a project's dependencies so its setup can be reproduced identically by anyone; **Pytest** runs small `assert`-based checks that catch broken behavior the instant it happens, not after.
- Your own files are modules too — `import grader` works exactly like `import math`, because Python draws no real distinction between "code you wrote" and "code you imported."

This closes Part 2's core language teaching. Part 3 moves from individual values into data structures — starting with lists — that every later AI lab in this program depends on.

---

*© 2026 Revature · AI Native Engineering — Foundations · Unit 2.5 · Version 1.0*

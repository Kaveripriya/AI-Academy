# Special Methods & Dataclasses

---

## 1. Learning Objectives

By the end of this unit, you will be able to:

✓ Explain why printing a plain object gives you an ugly, unhelpful default — and fix it with `__str__` and `__repr__`.  
✓ Implement `__eq__` so two objects with matching data are correctly treated as equal.  
✓ Refactor a hand-written class into a `@dataclass`, and explain exactly what work that decorator is doing for you.  
✓ Organise related classes into separate modules as a project grows.

---

## 2. Overview

By default, a Python object doesn't know how to describe itself, and it doesn't know what "equal" should mean for its own data — printing one gives you an ugly memory address instead of anything useful. **Special methods** — Python's name for a small set of methods with double-underscore names like `__str__` and `__eq__`, often called **dunder methods** ("dunder" = "double underscore") — are how you fix that: they teach an object to describe itself and compare itself sensibly.

Here's a real-world parallel: every museum faces the same problem with a new artifact sitting in a storage crate — it doesn't explain itself either. Someone has to write the placard that says what it is, and decide what makes two artifacts "the same piece" versus two separate copies. Writing special methods is writing that placard for your own objects. Once you've written one by hand, you'll meet `@dataclass` — a machine that writes several of these placards for you automatically, once you tell it what fields the object has.

---

## 3. Description

### 3.1 The Default Is Ugly — `__str__` and `__repr__`

Create a plain object and print it, with no special methods defined at all:

```python
class Student:
    def __init__(self, name, roll_number):
        self.name = name
        self.roll_number = roll_number

priya = Student("Priya", 101)
print(priya)
```

Output:

```
<__main__.Student object at 0x7f2a4c1b3d90>
```

That's Python being honest, not broken: without instructions, all it can tell you is the object's class and its memory address — genuinely useless for reading, logging, or debugging. This is exactly the "artifact with no placard" problem. You fix it with two methods:

- **`__str__`** — the friendly placard. Defines what `print()` and `str()` show: a readable description for a *human* reading it.
- **`__repr__`** — the curator's catalog card. Defines what shows up in a console, a log file, or inside a list of objects: precise enough that another programmer (or you, at 2 a.m. debugging) can tell exactly what the object contains.

```python
class Student:
    def __init__(self, name, roll_number):
        self.name = name
        self.roll_number = roll_number

    def __str__(self):
        return f"{self.name} (Roll No. {self.roll_number})"

    def __repr__(self):
        return f"Student(name={self.name!r}, roll_number={self.roll_number})"

priya = Student("Priya", 101)
print(priya)          # uses __str__
print(repr(priya))    # uses __repr__
```

Output:

```
Priya (Roll No. 101)
Student(name='Priya', roll_number=101)
```

Same object, two different jobs: `__str__` reads like a sentence for a person; `__repr__` reads like something you could paste back into Python to recreate the object.

### 3.2 Equal by Data, Not by Identity — `__eq__`

Create two *separate* `Student` objects holding identical data and compare them:

```python
priya_1 = Student("Priya", 101)
priya_2 = Student("Priya", 101)

print(priya_1 == priya_2)
```

Output:

```
False
```

That surprises almost everyone the first time. `==` without a custom rule checks **identity** — "are these literally the same object sitting in memory?" — not "do they hold the same data?" `priya_1` and `priya_2` are two separate crates that happen to contain identical labels; Python isn't opening the crates to compare labels, it's just checking whether you're pointing at the same crate twice.

**`__eq__`** lets you redefine what "equal" means for your own class — compare the data inside instead of the object's identity:

```python
class Student:
    def __init__(self, name, roll_number):
        self.name = name
        self.roll_number = roll_number

    def __eq__(self, other):
        return self.roll_number == other.roll_number

priya_1 = Student("Priya", 101)
priya_2 = Student("Priya", 101)

print(priya_1 == priya_2)
```

Output:

```
True
```

Now two students count as "equal" the moment their roll numbers match — a deliberate rule you chose, matching how two exam booklets are only considered the same submission because the roll number matches, not because they're physically the same sheet of paper.

### 3.3 Dataclasses — a Machine That Writes the Placard for You

Look back at 3.1 and 3.2: three methods (`__init__`, `__str__`/`__repr__`, `__eq__`) just to make one simple, data-holding class behave sensibly. For a class that's really just "a few named fields," writing all of that by hand every time is pure repetition — and repetition is exactly where copy-paste mistakes creep in.

`@dataclass`, from Python's built-in `dataclasses` module, is a **decorator** — a one-line instruction placed above a class that modifies what the class does — and this particular one automatically writes `__init__`, `__repr__`, and `__eq__` for you, based only on the fields you list.

**Before — hand-written, doing every job yourself:**

```python
class Student:
    def __init__(self, name, roll_number, marks):
        self.name = name
        self.roll_number = roll_number
        self.marks = marks

    def __repr__(self):
        return f"Student(name={self.name!r}, roll_number={self.roll_number}, marks={self.marks})"

    def __eq__(self, other):
        return (self.name, self.roll_number, self.marks) == (other.name, other.roll_number, other.marks)
```

**After — the same behaviour, written by the machine:**

```python
from dataclasses import dataclass

@dataclass
class Student:
    name: str
    roll_number: int
    marks: float

priya = Student("Priya", 101, 78.5)
priya_copy = Student("Priya", 101, 78.5)

print(priya)
print(priya == priya_copy)
```

Output:

```
Student(name='Priya', roll_number=101, marks=78.5)
True
```

Three type-hinted lines replaced eleven hand-written ones, and the behaviour — a readable `__repr__`, and `__eq__` comparing every field — is identical. The type hints (`str`, `int`, `float`) aren't optional decoration here; `@dataclass` reads them to know which fields exist and what belongs in `__init__`. Leave a field without a type hint and the decorator won't recognise it as a field at all.

Two things catch people off guard here. First, `@dataclass` generates `__repr__` and `__eq__`, but never `__str__` — printing a plain dataclass instance shows the same output `repr()` would, purely because of the fallback rule from §3.1, not because the decorator wrote a matching `__str__` for you. Second, if a field has a default value (`marks: float = 0.0`), every field declared *after* it must also have one — Python won't let a required field follow an optional one, because the generated `__init__` places fields as parameters in that same order:

```python
@dataclass
class Student:
    name: str
    marks: float = 0.0
    roll_number: int        # error: non-default field follows default field
```

Two optional arguments to the decorator itself are worth knowing: `@dataclass(frozen=True)` makes every field read-only after construction — real immutability, not the naming-convention-only kind from unit 4.2 — and `@dataclass(order=True)` additionally generates `<`, `<=`, `>`, `>=`, so a list of instances can be sorted directly.

### 3.4 Organising Classes into Modules

A **module** is just a `.py` file. As a project grows past one or two classes, cramming everything into a single file becomes its own problem — so related classes get grouped into their own module, and you `import` them where needed.

```python
# student.py
class Student:
    def __init__(self, name, roll_number):
        self.name = name
        self.roll_number = roll_number
```

```python
# main.py
from student import Student

priya = Student("Priya", 101)
print(priya.name)
```

Output:

```
Priya
```

`student.py` and `main.py` are two files in the same folder; `main.py` reaches into `student.py` by name. A larger project might have `student.py`, `faculty.py`, and `course.py` sitting side by side, each owning one idea.

---

## 4. Real-World Application

A crash log that shows `User(id=48213, email='a@x.com')` instead of a bare memory address is `__repr__` (§3.1) doing its job — giving an engineer enough detail to understand the object without re-running the program. The same mechanism, aimed at a human instead of a debugger, is what turns raw database fields into the clean profile card LinkedIn shows you: a `__str__`-style method formatting the object for reading, not debugging.

Two UPI payment receipts get flagged as "the same transaction" through a custom `__eq__` (§3.2) that compares transaction IDs — not whether they're literally the same object in memory. Two separately-fetched receipts for the same payment still need to count as equal, which is exactly the identity-vs-data distinction this unit opened with.

And a machine-learning config object holding `batch_size`, `learning_rate`, and `epochs` is almost always a dataclass (§3.3) — configuration is pure data with no custom behaviour, precisely the case `@dataclass` exists for.

---

## 5. Worked Example

**Goal:** You're building a small course-roster tool. Two `Student` records get created from two different data sources for the same real student — confirm the bug that causes them to compare as unequal, then fix it.

**1. Start with a class that has no `__eq__` at all.**

```python
class Student:
    def __init__(self, name, roll_number, marks):
        self.name = name
        self.roll_number = roll_number
        self.marks = marks
```

**2. Build "the same student" from two different sources and compare them.**

```python
from_registrar = Student("Priya", 101, 78.5)
from_exam_office = Student("Priya", 101, 78.5)

print(from_registrar == from_exam_office)
```

Output:

```
False
```

**3. Diagnose it.** Same name, same roll number, same marks — and still `False`, because without `__eq__`, Python is comparing whether these are the same object in memory. They aren't; they're two separate `Student` instances that happen to agree on every field. A roster-merging function using `==` to detect duplicates would silently keep both and double-count this student.

**4. Fix it — refactor to `@dataclass`, which writes a field-by-field `__eq__` for you.**

```python
from dataclasses import dataclass

@dataclass
class Student:
    name: str
    roll_number: int
    marks: float

from_registrar = Student("Priya", 101, 78.5)
from_exam_office = Student("Priya", 101, 78.5)

print(from_registrar == from_exam_office)
print(from_registrar)
```

Output:

```
True
Student(name='Priya', roll_number=101, marks=78.5)
```

*Common mistake: assuming two objects with "the same data" will compare equal by default. Without `__eq__` (written by hand or generated by `@dataclass`), Python only ever checks identity — always confirm which one you're getting before you rely on `==` to catch duplicates.*

---

## 6. Summary

- Printing a plain object gives you an unhelpful default (`<__main__.Student object at 0x...>`) — Python has no way to describe your data until you tell it how.
- **`__str__`** gives a human-readable description; **`__repr__`** gives a precise, debugging-friendly one — different audiences, different jobs.
- **`__eq__`** redefines `==` to compare data instead of identity; without it, two objects with identical fields still compare as `False`.
- **`@dataclass`** is a decorator that generates `__init__`, `__repr__`, and `__eq__` from type-hinted fields — the same behaviour as writing all three by hand, in a fraction of the code, provided every field carries a type hint.
- **Modules** (separate `.py` files) keep related classes organised as a project grows past a handful of classes.

This closes Part 4's coverage of classes and objects. The next module moves into files and exception handling — reading real data and managing the errors it inevitably produces.

---

*© 2026 Revature · AI Native Engineering — Foundations · Unit 4.3 · Version 1.0*

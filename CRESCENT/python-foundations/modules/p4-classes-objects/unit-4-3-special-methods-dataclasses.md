# Special Methods & Dataclasses

---

## 1. Learning Objectives

By the end of this unit, you will be able to:

✓ Explain the difference between `__repr__` and `__str__`, implement both, and explain what happens when only one is defined.  
✓ Implement `__eq__` so two objects with matching data compare as equal instead of falling back to identity.  
✓ Apply `@dataclass` to a class that's mostly attributes, and explain exactly which methods it generates — and which it doesn't.  
✓ Organize classes into their own modules and import them from another file.

---

## 2. Overview

A **special method** — also called a **dunder method**, for "double underscore" — is a method whose name begins and ends with `__`, such as `__init__`, `__str__`, `__repr__`, and `__eq__`. Python calls these implicitly, in response to a built-in operation, rather than you calling them by name. `__init__` already works this way: writing `Task(...)` calls it for you without you ever writing `t.__init__(...)` directly.

By default, an object has no useful way to describe itself, and no idea what "equal" should mean for its own data — printing one gives you a bare memory address, and `==` between two separately-built instances checks identity, not content. This unit covers the small set of special methods that fix that (`__repr__`, `__str__`, `__eq__`), then introduces `@dataclass` — a decorator that generates all three automatically once a class settles into being mostly a bundle of attributes.

---

## 3. Description

### 3.1 The Default Is Unhelpful

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

That's not broken — it's the default inherited from `object`, the base class every class ultimately extends. Without instructions, all it can report is the class and a memory address, which tells you the object exists but nothing about its state.

### 3.2 `__repr__` and `__str__` — Controlling How an Object Prints

**`__repr__`** is the special method responsible for an object's unambiguous, developer-facing representation — the string meant for someone debugging or logging. The convention is to make it look like the code that would recreate the object: `ClassName(field=value, ...)`.

```python
class Student:
    def __init__(self, name, roll_number):
        self.name = name
        self.roll_number = roll_number

    def __repr__(self):
        return f"Student(name={self.name!r}, roll_number={self.roll_number})"

priya = Student("Priya", 101)
print(priya)
```

Output:

```
Student(name='Priya', roll_number=101)
```

The `!r` inside `{self.name!r}` is a conversion flag: it calls `repr()` on that value instead of `str()`, which is why the name shows quote marks — matching how you'd actually type that string as Python source.

**`__str__`** is the human-readable, user-facing form. `print()`, `str()`, and f-strings prefer `__str__` over `__repr__` when both exist:

```python
class Student:
    def __init__(self, name, roll_number):
        self.name = name
        self.roll_number = roll_number

    def __repr__(self):
        return f"Student(name={self.name!r}, roll_number={self.roll_number})"

    def __str__(self):
        return f"{self.name} (Roll No. {self.roll_number})"

priya = Student("Priya", 101)
print(priya)
print(repr(priya))
```

Output:

```
Priya (Roll No. 101)
Student(name='Priya', roll_number=101)
```

One fallback rule is worth tracing carefully, because it explains behavior you'll see again with dataclasses: `object`'s own default `__str__` is implemented to simply call `self.__repr__()`. So a class that defines `__repr__` but not `__str__` still prints something useful — Python falls through to the inherited `__str__`, which calls your `__repr__`. Only when a class defines *both* does `print()` prefer the friendlier one.

### 3.3 `__eq__` — Equal by Data, Not by Identity

By default, `==` between two instances of a custom class behaves exactly like `is` — both check whether the two names refer to the *same object in memory*, not whether the two objects hold equal data:

```python
priya_1 = Student("Priya", 101)
priya_2 = Student("Priya", 101)

print(priya_1 == priya_2)
```

Output:

```
False
```

**`__eq__`** is the special method `==` calls implicitly, and defining it lets you decide what "equal" actually means for your class:

```python
class Student:
    def __init__(self, name, roll_number):
        self.name = name
        self.roll_number = roll_number

    def __eq__(self, other):
        return isinstance(other, Student) and self.roll_number == other.roll_number

priya_1 = Student("Priya", 101)
priya_2 = Student("Priya", 101)

print(priya_1 == priya_2)
```

Output:

```
True
```

`isinstance(other, Student)` earns its place first: `and` short-circuits left to right, so if `other` isn't even a `Student`, the expression returns `False` immediately without ever touching `other.roll_number` — which might not exist at all and would otherwise raise an `AttributeError` instead of a clean `False`.

### 3.4 `@dataclass` — Generating the Boilerplate

Three methods (`__init__`, `__str__`/`__repr__`, `__eq__`) just to make one simple, data-holding class behave sensibly is repetitive — and repetition is exactly where copy-paste mistakes creep in (forget to add a field to `__eq__` after adding it to `__init__`, and equality quietly stops checking it).

`@dataclass`, from Python's built-in `dataclasses` module, is a **decorator** that automatically generates `__init__`, `__repr__`, and `__eq__` for you, based only on the fields you list:

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

Three type-hinted lines replace what would otherwise be a hand-written `__init__`, `__repr__`, and `__eq__`. The type hints (`str`, `int`, `float`) aren't optional decoration — `@dataclass` reads them to know which fields exist. Leave a field without a type hint and the decorator won't recognize it as a field at all.

Two things catch people off guard. First, `@dataclass` generates `__repr__` and `__eq__`, but **never `__str__`** — printing a plain dataclass instance shows the same output `repr()` would, purely because of §3.2's fallback rule, not because the decorator wrote a matching `__str__`. Second, if a field has a default value, every field declared *after* it must also have one — a required field cannot follow an optional one, because the generated `__init__` places fields as parameters in that same order:

```python
@dataclass
class Student:
    name: str
    marks: float = 0.0
    roll_number: int        # error: non-default field follows default field
```

Two optional arguments to the decorator itself are worth knowing: `@dataclass(frozen=True)` makes every field read-only after construction — real immutability, not the naming-convention-only kind from encapsulation — and `@dataclass(order=True)` additionally generates `<`, `<=`, `>`, `>=`, so a list of instances can be sorted directly.

A dataclass is still an ordinary class underneath the decorator — you can add plain methods to it, and inheritance still works the same way, including subclassing it with a hand-written `__init__` that calls `super().__init__(...)` when the subclass needs extra construction logic the field-list shorthand can't express.

### 3.5 Organizing Classes into Modules

A **module** is just a `.py` file. As a project grows past one or two classes, cramming everything into a single file becomes its own problem — so related classes get grouped into their own module, and other files reach them with `import`.

```python
# student.py
from dataclasses import dataclass

@dataclass
class Student:
    name: str
    roll_number: int
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

`from student import Student` tells Python: locate a module named `student` (it finds `student.py` in the same directory), run that file once, and bind the name `Student` — the class object it defines — into this file's own namespace. A **module object** carries that namespace as a dictionary mapping every top-level name to the object it refers to. Import the same module a second time from anywhere else in the program, and Python skips re-running the file entirely, reusing the module object it already built. A larger project might have `student.py`, `faculty.py`, and `course.py` sitting side by side, each owning one idea — and a **package** extends this one level further: a directory holding several related modules, so other code can import from the group as a unit.

---

## 4. Real-World Application

A crash log that shows `User(id=48213, email='a@x.com')` instead of a bare memory address is `__repr__` doing its job — giving an engineer enough detail to understand the object without re-running the program. Two UPI payment receipts get flagged as "the same transaction" through a custom `__eq__` that compares transaction IDs, not whether they're literally the same object in memory — exactly the identity-vs-data distinction this unit opened with. And a machine-learning config object holding `batch_size`, `learning_rate`, and `epochs` is almost always a dataclass — configuration is pure data with no custom behavior, precisely the case `@dataclass` exists for.

---

## 5. Worked Example

**Goal:** Two `Student` records get created from two different data sources for the same real student — confirm the bug that causes them to compare as unequal, then fix it.

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

**3. Diagnose it.** Same name, same roll number, same marks — and still `False`, because without `__eq__`, Python compares whether these are the same object in memory. They aren't; they're two separate `Student` instances that happen to agree on every field. A roster-merging function using `==` to detect duplicates would silently keep both and double-count this student.

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

*Common mistake: assuming two objects with "the same data" will compare equal by default. Without `__eq__` — written by hand or generated by `@dataclass` — Python only ever checks identity; always confirm which one you're getting before you rely on `==` to catch duplicates.*

---

## 6. Summary

- Printing a plain object gives you an unhelpful default (`<__main__.Student object at 0x...>`) — Python has no way to describe your data until you tell it how.
- **`__repr__`** gives a precise, debugging-friendly representation; **`__str__`** gives a human-readable one. Without `__str__`, `print()` falls back to `__repr__`, because `object`'s default `__str__` calls `self.__repr__()`.
- **`__eq__`** redefines `==` to compare data instead of identity; without it, two objects with identical fields still compare as `False`.
- **`@dataclass`** generates `__init__`, `__repr__`, and `__eq__` from type-hinted fields — but never `__str__` — provided every field carries a type hint, and every defaulted field comes after every required one.
- **Modules** (separate `.py` files) keep related classes organized as a project grows; `import`/`from module import Name` reaches a class defined elsewhere without redefining it.

This closes Part 4's coverage of classes and objects. The next module moves into files and exception handling — reading real data and managing the errors it inevitably produces.

---

*© 2026 Revature · AI Native Engineering — Foundations · Unit 4.3 · Version 1.0*

# Special Methods & Dataclasses

---

## 1. Learning Objectives

By the end of this unit, you will be able to:

✓ Implement `__str__` and `__repr__` to control how an object is displayed.  
✓ Implement `__eq__` to compare two objects for equality.  
✓ Refactor a class into a dataclass using `@dataclass`.  
✓ Explain how dataclasses reduce boilerplate code.  
✓ Organise related classes into separate modules for a larger project.

---

## 2. Overview

Every lab sample bottle in a science lab carries a label describing exactly what is inside it, so anyone picking it up instantly understands what they are holding. Python objects can carry the same kind of label — special methods that control how an object describes itself when printed, and how it is compared against another object.

These special methods are recognisable by their double-underscore names, such as `__str__` and `__eq__`, often called **dunder methods** ("double underscore"). Two exam answer booklets are only considered "equal" if their roll numbers match — not because they are the same physical booklet — and `__eq__` lets you define exactly that kind of custom equality rule for your own classes.

This unit also introduces **dataclasses** — a shortcut that writes several of these common methods for you automatically, cutting down the repetitive boilerplate code you would otherwise write by hand for every simple data-holding class.

By the end of this unit, you will be able to write classes that behave predictably when printed, compared, and organised across multiple files — exactly the habits expected in any real, multi-file AI or software project.

---

## 3. Description

### 3.1 Operator Overloading — Dunder Methods

- **`__str__`** — defines the readable text Python shows when you `print()` an object.
- **`__repr__`** — defines the more precise, developer-facing text Python shows in a console or log, ideally detailed enough to recreate the object.
- **`__eq__`** — defines what it means for two objects of this class to be considered equal with `==`.

```python
class Student:
    def __init__(self, name, roll_number):
        self.name = name
        self.roll_number = roll_number

    def __str__(self):
        return f"{self.name} (Roll No. {self.roll_number})"

    def __repr__(self):
        return f"Student(name={self.name!r}, roll_number={self.roll_number})"

    def __eq__(self, other):
        return self.roll_number == other.roll_number

priya_1 = Student("Priya", 101)
priya_2 = Student("Priya", 101)

print(priya_1)
print(repr(priya_1))
print(priya_1 == priya_2)
```

**Output:**
```
Priya (Roll No. 101)
Student(name='Priya', roll_number=101)
True
```

Without `__eq__`, `priya_1 == priya_2` would return `False` by default, since Python would compare whether they are the exact same object in memory, not whether their data matches.

### 3.2 Dataclasses

- **`@dataclass`** — a decorator from the `dataclasses` module that automatically generates `__init__`, `__repr__`, and `__eq__` for a class, based only on the attributes you declare.
- Dataclasses remove the need to hand-write these methods for simple, data-holding classes.

```python
from dataclasses import dataclass

@dataclass
class Student:
    name: str
    roll_number: int
    marks: float

priya = Student("Priya", 101, 78.5)
rohan = Student("Priya", 101, 78.5)

print(priya)
print(priya == rohan)
```

**Output:**
```
Student(name='Priya', roll_number=101, marks=78.5)
True
```

Compare this to the hand-written `Student` class in 3.1 — `@dataclass` produced a working `__init__`, `__repr__`, and `__eq__` from three lines of type-hinted attributes, with no manual method bodies at all.

### 3.3 Organising Classes into Modules

- A **module** is simply a `.py` file; related classes are usually grouped into the same module, and unrelated groups of classes go into separate modules.
- Classes from one module are brought into another using `import`.

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

**Output:**
```
Priya
```

As a project grows, organising classes into their own modules — one file for `student.py`, another for `faculty.py` — keeps the codebase navigable instead of one enormous file.

---

## 4. Real-World Application

| **Where you see it** | **How Python is working behind the scenes** |
|---|---|
| **A LinkedIn profile card** | The clean summary you see (name, headline, photo) is produced by a `__str__`-style method formatting the underlying profile object for display. |
| **Comparing two UPI transaction receipts** | An `__eq__` method checks whether two transaction objects share the same transaction ID, not whether they are the same object in memory. |
| **Error logs in a production AI system** | Engineers rely on a clear `__repr__` so that when an object appears in a log file, its exact state is immediately readable for debugging. |
| **Configuration objects in ML pipelines** | Simple settings objects (batch size, learning rate) are frequently written as dataclasses, since they are mostly just data with little custom behaviour. |
| **A Django or Flask web project** | Real projects organise models like `User`, `Post`, and `Comment` into separate modules/files, exactly as shown in 3.3, so the codebase stays maintainable as it grows. |

---

## 5. Worked Example

**Scenario:** Your professor asks you to refactor your `Student` class from earlier units into a dataclass for a mini project, and confirm it still prints cleanly and compares correctly.

**1. Original hand-written class (for comparison).**
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

**2. Refactor using `@dataclass`.**
```python
from dataclasses import dataclass

@dataclass
class Student:
    name: str
    roll_number: int
    marks: float
```

**3. Confirm the behaviour is identical.**
```python
priya = Student("Priya", 101, 78.5)
priya_copy = Student("Priya", 101, 78.5)

print(priya)
print(priya == priya_copy)
```

**Output:**
```
Student(name='Priya', roll_number=101, marks=78.5)
True
```

*Common mistake: adding a type hint without a value, like `marks` instead of `marks: float`, inside a `@dataclass` — every attribute must be annotated with a type (`str`, `int`, `float`, and so on), or Python will not recognise it as a dataclass field.*

---

## 6. Summary

- **`__str__`** controls the readable text shown when an object is printed; **`__repr__`** controls the more precise, developer-facing text.
- **`__eq__`** defines what makes two objects of a class equal, instead of relying on Python's default identity check.
- **`@dataclass`** automatically generates `__init__`, `__repr__`, and `__eq__` from a class's type-hinted attributes, removing repetitive boilerplate.
- **Modules** — separate `.py` files — keep related classes organised as a project grows, imported wherever they are needed.

This closes Part A's coverage of core Python. The next module moves into files and exception handling — reading real data and managing the errors it inevitably produces.

---

*© 2026 Revature · AI Native Engineering — Foundations · Unit 4.3 · Version 1.0*

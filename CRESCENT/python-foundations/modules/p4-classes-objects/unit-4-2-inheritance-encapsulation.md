# Inheritance & Encapsulation

---

[← Previous: 4.1 Object-Oriented Foundations](unit-4-1-object-oriented-foundations.md) | [Go back to TOC](../../README.md) | [Next: 4.3 Special Methods & Dataclasses →](unit-4-3-special-methods-dataclasses.md)

## 1. Learning Objectives

By the end of this unit, you will be able to:

- **Explain** what inheritance is and how a subclass automatically gains the attributes and methods of its superclass without copying any code.
- **Implement** single-level and multi-level inheritance using `super()` to extend a superclass's behavior instead of duplicating it.
- **Differentiate** single inheritance, multi-level inheritance, and multiple inheritance, and describe how Method Resolution Order (MRO) decides which class's method runs.
- **Analyze** a diamond-shaped class hierarchy by reading `ClassName.__mro__` to predict exactly which method executes and in what order.
- **Apply** Python's underscore naming conventions (`_name` and `__name`) to encapsulate an object's internal state.
- **Debug** the most common inheritance mistake — forgetting to call `super().__init__()` — and explain exactly why it causes an `AttributeError`.

---

## 2. Overview

In Unit 4.1, you built a `Student` class with `name`, `roll_number`, `marks`, and a `has_passed()` method. Every real class you write from here on will rarely stand alone — it will build on something that already exists. **Inheritance** is Python's mechanism for saying "this new class is like that existing class, plus a little extra," without retyping a single line of the original class's code.

Think about how large-scale Indian software systems are actually built. A banking application does not write one giant `Account` class that handles savings accounts, current accounts, and loan accounts all at once — it writes one shared `BankAccount` class, and lets `SavingsAccount` and `CurrentAccount` extend it, each adding only what makes it different. A food delivery platform does the same with `DeliveryPartner`, `BikePartner`, and `CarPartner`. This is exactly the "is-a" relationship inheritance is built for: a `SavingsAccount` **is a** `BankAccount`, plus interest.

This unit takes that idea further than a single parent-child pair. You will chain inheritance across multiple levels, see what happens when a class inherits from more than one parent at once (and how Python's **Method Resolution Order** resolves any ambiguity that creates), and learn **encapsulation** — the naming conventions Python gives you to mark which attributes are safe for outside code to touch, and which are internal bookkeeping that should be left alone.

---

## 3. Description

### 3.1 Definition

**Inheritance** is a mechanism where a new class is defined in terms of an existing class, automatically acquiring all of that existing class's attributes and methods. The existing class being extended is called the **superclass** (also called the **parent class** or **base class**); the new class built on top of it is called the **subclass** (also called the **child class** or **derived class**).

**Encapsulation** is the practice of bundling an object's data together with the methods that operate on it, while restricting which parts of that data outside code is meant to access directly. In Python, encapsulation is expressed through naming convention — a leading underscore signals "internal, please don't touch" — rather than through a hard language rule.

```python
class BankAccount:               # superclass / parent / base class
    def __init__(self, balance):
        self._balance = balance


class SavingsAccount(BankAccount):   # subclass / child / derived class
    pass
```

Even though `SavingsAccount` has an empty body, it already has everything `BankAccount` has. That single line, `class SavingsAccount(BankAccount):`, is the entire mechanism of inheritance.

### 3.2 Why This Concept Exists

Without inheritance, every related class would need to be written from scratch, and every future change to shared behavior would have to be copy-pasted into every class that used it. Inheritance exists to solve three real problems that show up constantly in Indian IT projects:

- **Avoiding duplication** — a `CurrentAccount` and a `SavingsAccount` both need `deposit()` and `withdraw()`; writing that logic once on a shared `BankAccount` and inheriting it means one bug fix in one place fixes it everywhere.
- **Modeling real-world relationships** — a `Manager` genuinely **is an** `Employee`, with extra responsibilities. Inheritance lets your code mirror that real relationship directly.
- **Extending safely** — `super()` lets a subclass build on top of a superclass's method without needing to know or retype its internals, so the superclass can change later without breaking every subclass.

Encapsulation exists for a related but distinct reason: to protect an object's internal state from being changed carelessly by code outside the class. A bank account's balance should only ever change through `deposit()` and `withdraw()` — never by some unrelated piece of code directly overwriting it. Naming a field `_balance` or `__balance` signals that intention clearly to every other developer reading the code, even though, as you'll see in §3.7, Python does not physically stop anyone from ignoring that signal.

### 3.3 Key Terminology

| Term | Simple Meaning |
|---|---|
| **Inheritance** | A mechanism where a new class automatically acquires the attributes and methods of an existing class. |
| **Superclass (parent/base class)** | The existing class being extended. |
| **Subclass (child/derived class)** | The new class built on top of a superclass, using `class Child(Parent):`. |
| **`super()`** | A built-in function that gives a subclass access to the next class in line (usually its superclass), so it can call that class's methods instead of duplicating them. |
| **Overriding** | A subclass defining its own version of a method the superclass already has; the subclass's version runs instead of the superclass's. |
| **Multi-level inheritance** | A chain of more than two classes, where each class extends the one before it (e.g., `A` → `B` → `C`). |
| **Multiple inheritance** | A single subclass extending more than one direct superclass at once, written `class C(A, B):`. |
| **MRO (Method Resolution Order)** | The fixed order Python searches through a class's ancestors when looking up a method or attribute; inspectable via `ClassName.__mro__`. |
| **Diamond problem** | The situation where two superclasses share a common ancestor, and a subclass inherits from both — raising the question of which ancestor's method runs first. |
| **Encapsulation** | Bundling data and the methods that act on it together, while signaling which parts are meant to stay internal to the class. |
| **Public attribute** | A normal attribute (`balance`) — no naming signal; any code may read or change it freely. |
| **Protected attribute (`_name`)** | A single leading underscore — a convention meaning "internal use, don't rely on this from outside," but not enforced by Python. |
| **Private attribute (`__name`)** | A double leading underscore — triggers **name mangling**, rewriting the attribute to `_ClassName__name`. |
| **Name mangling** | Python's automatic rewriting of `self.__x` inside a class body to `self._ClassName__x`, used to avoid attribute name collisions across a hierarchy. |
| **`isinstance()`** | A built-in function that checks whether an object belongs to a given class or any of its superclasses. |
| **`issubclass()`** | A built-in function that checks the same relationship at the class level, not on an instance. |

### 3.4 Syntax

```python
class Child(Parent):
    def __init__(self, ...):
        super().__init__(...)
        # new attributes specific to Child

class C(A, B):
    pass
```

| Part | What it is | Why it's there |
|---|---|---|
| `class Child(Parent):` | Declares `Child` as a subclass of `Parent`. | This single line grants `Child` every attribute-setting and method `Parent` has. |
| `super()` | A proxy referring to the next class in the MRO — usually the superclass. | Lets a subclass call the superclass's method without naming it explicitly, so future changes to `Parent` are picked up automatically. |
| `super().__init__(...)` | Calls the superclass's constructor from inside the subclass's own `__init__`. | Sets up every attribute the superclass is responsible for, so the subclass doesn't have to retype that logic. |
| `class C(A, B):` | Declares `C` as a subclass of **both** `A` and `B` — multiple inheritance. | Lets one class combine behavior from more than one independent superclass. |
| `_name` | A single leading underscore on an attribute or method name. | Signals "protected — internal use only," by convention. |
| `__name` | A double leading underscore on an attribute or method name. | Triggers name mangling to `_ClassName__name`, mainly to avoid accidental name collisions across a hierarchy. |

### 3.5 Rules

- A subclass is declared with `class Child(Parent):`; the parenthesized name(s) are the direct superclass(es).
- If a subclass does not define its own `__init__`, Python uses the superclass's `__init__` automatically.
- If a subclass **does** define its own `__init__`, the superclass's `__init__` does **not** run automatically — it must be called explicitly with `super().__init__(...)`.
- Method lookup always follows the MRO: Python checks the object's own class first, then walks the MRO in order until it finds the method.
- `super()` always means "the next class in the computed MRO," not literally "my parent class" — this distinction only becomes visible with multiple inheritance (see §3.10).
- A double leading underscore (`__name`) is rewritten by Python, at compile time, to `_ClassName__name`, using the exact name of the class where that line of code is written.
- `isinstance(obj, Cls)` returns `True` if `Cls` appears anywhere in the object's class's MRO, not only if it is the immediate class.

### 3.6 Best Practices

- Favor **composition over deep inheritance chains** — if a relationship isn't genuinely "is-a" (a `SavingsAccount` **is a** `BankAccount`), consider giving one class an instance of another instead of forcing an inheritance relationship that doesn't really fit.
- Keep hierarchies shallow. Two or three levels are usually enough; a chain five levels deep becomes hard to trace and debug.
- Always call `super().__init__()` at the start of a subclass's `__init__`, before adding anything new — this guarantees the superclass's part of the object is fully built first.
- Use a single leading underscore (`_balance`) as your default way to mark internal attributes; reach for a double leading underscore only when you specifically need to avoid a name collision across a hierarchy.
- Prefer well-defined methods (like `deposit()`, `withdraw()`) over direct attribute access, even for attributes without any underscore — it keeps validation logic in one place.
- When using multiple inheritance, keep each parent class narrowly focused on one responsibility (often called a **mixin**), so the MRO stays predictable.

### 3.7 Common Mistakes

- **Forgetting to call `super().__init__()`** — the superclass's attributes are never set, and any method relying on them later fails with an `AttributeError`.
- **Assuming a subclass "automatically" has the parent's data** — inheriting a *method* only makes it available; the object's actual *data* exists only if `__init__` genuinely ran and assigned it.
- **Diamond-problem confusion in multiple inheritance** — assuming `super()` inside a class always jumps to "its" direct parent; it actually jumps to the next class in the MRO, which in a diamond shape is often a sibling class, not the shared ancestor.
- **Assuming Python enforces true private variables** — `self.__pin` is still reachable from outside as `self._ClassName__pin`; double underscore prevents accidental name collisions, it does not provide real security.
- **Building unnecessarily deep inheritance chains** just to reuse a couple of methods, when a simpler, flatter design (or composition) would be easier to read and maintain.
- **Overriding a method without knowing you're overriding it** — accidentally reusing a superclass's method name and silently losing access to its original behavior.

### 3.8 Comparison Table: Single Inheritance vs Multi-Level vs Multiple Inheritance

| Aspect | Single Inheritance | Multi-Level Inheritance | Multiple Inheritance |
|---|---|---|---|
| Structure | One subclass, one direct superclass | A chain: `A` → `B` → `C`, each extending the one before | One subclass, two or more direct superclasses at the same level |
| Syntax | `class B(A):` | `class B(A):` then `class C(B):` | `class C(A, B):` |
| `super()` behavior | Always resolves to the one superclass | Each level's `super()` resolves to the class directly above it in the chain | Resolves to the next class in the computed MRO, which may be a sibling, not a shared ancestor |
| Main risk | Very low — straightforward to reason about | Chains that grow too long become hard to trace | The diamond problem — ambiguity about method order, resolved by MRO |
| Typical use | A specific case of a general class (`SavingsAccount` from `BankAccount`) | Layered specialization (`Employee` → `Manager` → `SeniorManager`) | Combining independent behaviors (mixins) into one class |

### 3.9 Comparison Table: Public vs Protected vs Private Naming Convention

| Naming Style | Example | Meaning | Enforced by Python? |
|---|---|---|---|
| Public | `self.balance` | No restriction signaled; any code may read or write it freely. | N/A — this is the default |
| Protected (`_name`) | `self._balance` | Convention: "internal use — don't rely on this from outside code." | No — purely a social agreement between developers |
| Private (`__name`) | `self.__pin` | Triggers name mangling to `self._ClassName__pin`, mainly to prevent accidental name collisions across a class hierarchy. | Partially — the original name stops working, but the mangled name is still fully accessible |

### 3.10 Diagram: Class Hierarchy and MRO

```mermaid
flowchart BT
    BASE["<b>BankAccount</b><br/><span style='font-size:11px;color:#6d28d9'>__init__, deposit(), show_balance()</span>"]:::done
    SAVE["<b>SavingsAccount</b><br/><span style='font-size:11px;color:#6d28d9'>extends BankAccount<br/>adds interest_rate</span>"]:::auto
    SMS["<b>SMSAlertMixin</b><br/><span style='font-size:11px;color:#6d28d9'>adds describe()</span>"]:::auto
    PREMIUM["<b>PremiumSavingsAccount</b><br/><span style='font-size:11px;color:#6d28d9'>class PremiumSavingsAccount(SavingsAccount, SMSAlertMixin)</span>"]:::start
    MRO["<b>__mro__ order</b><br/><span style='font-size:11px;color:#6d28d9'>PremiumSavingsAccount, SavingsAccount, BankAccount, SMSAlertMixin, object</span>"]:::ghost

    PREMIUM -- "inherits" --> SAVE
    PREMIUM -- "inherits" --> SMS
    SAVE -- "inherits" --> BASE
    PREMIUM -. "__mro__" .-> MRO

    classDef start fill:#a5d8ff,stroke:#4a9eed,stroke-width:2px
    classDef auto fill:#d0bfff,stroke:#8b5cf6,stroke-width:2px
    classDef done fill:#b2f2bb,stroke:#22c55e,stroke-width:2px
    classDef ghost fill:none,stroke:none,color:#6d28d9
```

This diagram shows a realistic banking hierarchy: `SavingsAccount` extends `BankAccount` through ordinary single inheritance, while `PremiumSavingsAccount` uses **multiple inheritance** to combine `SavingsAccount` with an unrelated `SMSAlertMixin`. Python computes the `__mro__` the moment `PremiumSavingsAccount` is defined — it searches `SavingsAccount`'s own chain fully before moving to `SMSAlertMixin`, which is why `BankAccount` appears before `SMSAlertMixin` in the order, even though `SMSAlertMixin` was written second in the class definition.

### 3.11 Code Examples

**Basic example** — single-level inheritance with overriding:

```python
class Animal:
    def __init__(self, name):
        self.name = name

    def speak(self):
        return f"{self.name} makes a sound."


class Dog(Animal):
    def speak(self):
        return f"{self.name} barks."


a = Animal("Generic Animal")
d = Dog("Tommy")
print(a.speak())
print(d.speak())
```

*Line-by-line explanation:*
- `class Animal:` defines the superclass with an `__init__` that stores `name`, and a `speak()` method.
- `class Dog(Animal):` declares `Dog` as a subclass of `Animal` — it inherits `__init__` automatically, since it doesn't define its own.
- `def speak(self):` inside `Dog` **overrides** `Animal`'s version — Python finds `Dog`'s own `speak()` first and never looks further up.
- `d = Dog("Tommy")` runs `Animal.__init__` (inherited, not overridden) to set `self.name = "Tommy"`.
- Output:
  ```
  Generic Animal makes a sound.
  Tommy barks.
  ```

**Beginner example** — extending `__init__` with `super()`:

```python
class Vehicle:
    def __init__(self, brand, speed):
        self.brand = brand
        self.speed = speed

    def describe(self):
        return f"{self.brand} moving at {self.speed} km/h"


class Car(Vehicle):
    def __init__(self, brand, speed, fuel_type):
        super().__init__(brand, speed)
        self.fuel_type = fuel_type

    def describe(self):
        base = super().describe()
        return f"{base}, running on {self.fuel_type}"


c = Car("Tata", 90, "Diesel")
print(c.describe())
```

*Line-by-line explanation:*
- `class Car(Vehicle):` makes `Car` a subclass of `Vehicle`.
- `super().__init__(brand, speed)` calls `Vehicle.__init__`, setting `self.brand` and `self.speed` — without retyping that logic.
- `self.fuel_type = fuel_type` adds the one new attribute that only `Car` needs.
- `describe()` on `Car` **extends** `Vehicle`'s version: `super().describe()` gets `Vehicle`'s string first, then `Car` appends its own detail — this is extending, not plain overriding.
- Output: `Tata moving at 90 km/h, running on Diesel`

**Practical example** — multi-level inheritance (three classes deep):

```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def introduce(self):
        return f"I am {self.name}, {self.age} years old."


class Employee(Person):
    def __init__(self, name, age, employee_id):
        super().__init__(name, age)
        self.employee_id = employee_id

    def introduce(self):
        base = super().introduce()
        return f"{base} My employee ID is {self.employee_id}."


class Manager(Employee):
    def __init__(self, name, age, employee_id, team_size):
        super().__init__(name, age, employee_id)
        self.team_size = team_size

    def introduce(self):
        base = super().introduce()
        return f"{base} I manage a team of {self.team_size}."


m = Manager("Priya Sharma", 34, "EMP2026101", 8)
print(m.introduce())
print(isinstance(m, Person))
print(isinstance(m, Employee))
print(Manager.__mro__)
```

*Line-by-line explanation:*
- Three classes chain in a straight line: `Person` → `Employee` → `Manager` — this is **multi-level inheritance**, not multiple inheritance, because each class has exactly one direct parent.
- Each subclass's `__init__` calls `super().__init__(...)` first, passing along only the fields the level above needs, then adds its own new field (`employee_id`, then `team_size`).
- Each `introduce()` calls `super().introduce()` first and appends its own sentence — the final string is assembled one level at a time, all the way from `Person` up to `Manager`.
- `isinstance(m, Person)` and `isinstance(m, Employee)` both report `True`, because `Manager.__mro__` includes every ancestor in the chain, not just the immediate parent.
- Output:
  ```
  I am Priya Sharma, 34 years old. My employee ID is EMP2026101. I manage a team of 8.
  True
  True
  (<class '__main__.Manager'>, <class '__main__.Employee'>, <class '__main__.Person'>, <class 'object'>)
  ```

**Industry-oriented example** — banking hierarchy with encapsulation and multiple inheritance:

```python
class BankAccount:
    def __init__(self, account_holder, balance):
        self.account_holder = account_holder
        self._balance = balance        # protected: internal bookkeeping
        self.__pin = "0000"            # private: name-mangled

    def deposit(self, amount):
        self._balance += amount
        return self._balance

    def show_balance(self):
        return f"{self.account_holder}'s balance: Rs. {self._balance}"


class SavingsAccount(BankAccount):
    def __init__(self, account_holder, balance, interest_rate):
        super().__init__(account_holder, balance)
        self.interest_rate = interest_rate

    def add_interest(self):
        interest = self._balance * self.interest_rate / 100
        self._balance += interest
        return self._balance


class SMSAlertMixin:
    def describe(self):
        return "Sends SMS alerts on every transaction"


class PremiumSavingsAccount(SavingsAccount, SMSAlertMixin):
    pass


acc = PremiumSavingsAccount("Rohit Verma", 50000, 4)
acc.add_interest()
print(acc.show_balance())
print(acc.describe())
print(acc._balance)                      # works — protected, only a convention
print(acc._BankAccount__pin)             # works — mangled name, still reachable
print(PremiumSavingsAccount.__mro__)
```

*Line-by-line explanation:*
- `BankAccount` stores `_balance` with a single underscore (protected: internal, but reachable) and `__pin` with a double underscore (triggers name mangling to `_BankAccount__pin`).
- `SavingsAccount(BankAccount)` extends `BankAccount` through single inheritance, calling `super().__init__(...)` to set up the base account, then adding `interest_rate`.
- `SMSAlertMixin` is an independent class with no relation to `BankAccount` — it exists purely to add one extra piece of behavior.
- `class PremiumSavingsAccount(SavingsAccount, SMSAlertMixin):` uses **multiple inheritance** to combine both — `PremiumSavingsAccount` gets everything from `SavingsAccount` (and, through it, `BankAccount`) plus `describe()` from `SMSAlertMixin`.
- `acc._balance` works from outside the class — proving the protected underscore is convention, not enforcement.
- `acc._BankAccount__pin` works too — proving the "private" double underscore only rewrote the name, it did not truly hide it.
- `PremiumSavingsAccount.__mro__` shows the exact search order Python computed: `PremiumSavingsAccount`, `SavingsAccount`, `BankAccount`, `SMSAlertMixin`, `object` — `BankAccount` is fully resolved before Python ever reaches `SMSAlertMixin`.
- Output:
  ```
  Rohit Verma's balance: Rs. 52000.0
  Sends SMS alerts on every transaction
  52000.0
  0000
  (<class '__main__.PremiumSavingsAccount'>, <class '__main__.SavingsAccount'>, <class '__main__.BankAccount'>, <class '__main__.SMSAlertMixin'>, <class 'object'>)
  ```

---

## 4. Real-World Application

- **Banking & FinTech:** `SavingsAccount` and `CurrentAccount` both extend a shared `BankAccount` base, reusing `deposit()`/`withdraw()` logic while each adds its own rules (interest, overdraft limits) — exactly the pattern in §3.11's industry example.
- **UPI / Payment Systems:** A payment gateway might have a base `PaymentMethod` class, extended by `UPIPayment`, `CardPayment`, and `NetBankingPayment`, each overriding a `process()` method with its own validation logic while sharing common logging and retry behavior.
- **E-commerce:** A `Product` base class is extended by `ElectronicsProduct` and `GroceryProduct`, each adding fields like `warranty_period` or `expiry_date`, while both inherit shared pricing and discount logic.
- **Food Delivery:** A `DeliveryPartner` base class is extended by `BikePartner` and `CarPartner`; combining a partner class with an independent `RatingMixin` through multiple inheritance is a realistic use of the MRO concept from §3.10.
- **Healthcare:** A `Patient` base class is extended by `InpatientRecord` and `OutpatientRecord`, each adding fields specific to that kind of visit while sharing common demographic fields through inheritance.
- **Railway Booking (IRCTC-style systems):** A `Passenger` base class extended by `SeniorCitizenPassenger` or `TatkalBooking`, each overriding fare-calculation logic while reusing shared booking and cancellation methods.
- **Exception Hierarchies:** Python's own built-in errors form exactly this structure — `ValueError` and `TypeError` both extend `Exception` — and production code routinely extends further, e.g., `InvalidPinError(ValidationError)`, so `isinstance(err, ValidationError)` catches every specific subtype without checking each one by name.

---

## 5. Worked Example

### Problem Statement

You already have a `Student` class from Unit 4.1 with `name`, `roll_number`, `marks`, and `has_passed()`. You are asked to build a `GraduateStudent` class that reuses everything `Student` already provides, adds one new field — `thesis_topic` — and raises the passing mark to 50. You must then deliberately break the pattern by skipping `super().__init__()` in a second class, to see exactly what goes wrong and why.

### Step 1: Understand the Problem

`GraduateStudent` needs everything `Student` has (`name`, `roll_number`, `marks`) plus one new field (`thesis_topic`), and its own, higher passing threshold. Nothing about `Student`'s existing code should be retyped — it should be reused through inheritance and extended through `super()`.

### Step 2: Plan the Solution

Declare `GraduateStudent(Student)`. In its `__init__`, call `super().__init__(name, roll_number, marks)` to let `Student` set up its three fields, then set `self.thesis_topic` separately. Override `has_passed()` with `GraduateStudent`'s own version, since the passing rule genuinely differs — no need for `super()` there. Then write a second class, `BrokenGraduateStudent`, that forgets to call `super().__init__()`, to observe the failure directly.

### Step 3: Write the Python Code

```python
class Student:
    def __init__(self, name, roll_number, marks):
        self.name = name
        self.roll_number = roll_number
        self.marks = marks

    def has_passed(self):
        return self.marks >= 40


class GraduateStudent(Student):
    def __init__(self, name, roll_number, marks, thesis_topic):
        super().__init__(name, roll_number, marks)
        self.thesis_topic = thesis_topic

    def has_passed(self):
        return self.marks >= 50   # graduate-level pass mark is higher


class BrokenGraduateStudent(Student):
    def __init__(self, name, roll_number, marks, thesis_topic):
        self.thesis_topic = thesis_topic   # forgot to call super().__init__()


gs = GraduateStudent("Arjun", 301, 88.0, "Computer Vision for Traffic Systems")
print(gs.name)
print(gs.has_passed())
print(isinstance(gs, Student))

broken = BrokenGraduateStudent("Meera", 302, 91.0, "Speech Recognition")
print(broken.thesis_topic)
print(broken.has_passed())
```

### Step 4: Explain Each Line

- `class Student:` and its `__init__`/`has_passed()` are unchanged from Unit 4.1 — the existing superclass.
- `class GraduateStudent(Student):` declares the subclass.
- `super().__init__(name, roll_number, marks)` calls `Student.__init__`, which sets `self.name`, `self.roll_number`, and `self.marks` on the new object.
- `self.thesis_topic = thesis_topic` adds the one field unique to `GraduateStudent`.
- `def has_passed(self):` inside `GraduateStudent` **overrides** `Student`'s version with a stricter rule — no `super()` call is needed here because the entire rule is replaced, not extended.
- `class BrokenGraduateStudent(Student):` also declares a subclass, but its `__init__` **never calls `super().__init__()`** — only `self.thesis_topic` gets set.
- `gs.name`, `gs.has_passed()`, and `isinstance(gs, Student)` all work correctly on the properly built object.
- `broken.thesis_topic` works, because that line genuinely ran.
- `broken.has_passed()` needs `self.marks`, but `self.marks` was never set — `Student.__init__` never ran — so this line fails.

### Step 5: Sample Input

The four hardcoded values passed into `GraduateStudent(...)` and `BrokenGraduateStudent(...)`: `"Arjun"`, `301`, `88.0`, `"Computer Vision for Traffic Systems"`, and `"Meera"`, `302`, `91.0`, `"Speech Recognition"`. No user input is involved.

### Step 6: Expected Output

```
Arjun
True
True
Speech Recognition
AttributeError: 'BrokenGraduateStudent' object has no attribute 'marks'
```

### Step 7: Why the Output Is Produced

`gs.name` prints correctly because `super().__init__()` ran and set it. `gs.has_passed()` returns `True` because `GraduateStudent`'s overridden rule (`marks >= 50`) checks `88.0`, which passes easily. `isinstance(gs, Student)` returns `True` because `Student` appears in `GraduateStudent`'s MRO, regardless of the override.

For `broken`, `thesis_topic` prints fine because that assignment genuinely executed. But `has_passed()` needs `self.marks` — and since `Student.__init__` never ran (there was no `super().__init__()` call), that attribute was never created, so Python raises `AttributeError`. This proves that inheriting a *method* only makes it available; the object's actual *data* exists only when `__init__` genuinely runs and sets it.

---

### Important Notes (Interview Insights)

- **MRO and `super()` are classic interview topics** for any Python role. Be ready to explain, in your own words, that `super()` does not mean "my direct parent" — it means "the next class in the MRO" — and be able to trace `ClassName.__mro__` for a small diamond-shaped hierarchy on a whiteboard.
- A very common fresher interview question: *"Does Python have private variables like Java?"* The confident, correct answer: **no** — Python's underscore convention is "convention, not enforcement." A single underscore (`_name`) is a social signal only; a double underscore (`__name`) triggers name mangling, which prevents accidental collisions but can still be bypassed by anyone who knows the mangled name. This is a fundamentally different model from Java's `private` keyword, which the compiler actively enforces.
- Interviewers often ask you to distinguish **overriding** (replacing a method entirely) from **extending** (calling `super()` to reuse the superclass's version and add to it) — know both terms and be able to demonstrate each with a one-line code example.
- Be ready to explain why `isinstance()` is generally preferred over checking `type(obj) == SomeClass` — `isinstance()` respects the whole inheritance hierarchy (and MRO), while an exact `type()` comparison does not.

---

## 6. Key Takeaways

- **Inheritance** (`class Child(Parent):`) gives a subclass every attribute-setting and method the superclass has, automatically — no code duplication needed.
- **`super()`** calls the next class in the object's Method Resolution Order — not necessarily a hardcoded "parent" — letting a subclass extend inherited logic instead of duplicating it.
- **Multi-level inheritance** chains classes (`A` → `B` → `C`); each level only needs to call `super()` once, targeting the level directly above it.
- **Multiple inheritance** (`class C(A, B):`) lets one class combine behavior from more than one direct superclass, at the cost of needing to understand MRO.
- **MRO (Method Resolution Order)** is the fixed search order Python computes once, at class definition time; inspect it any time with `ClassName.__mro__`.
- The **diamond problem** shows that `super()` means "next in the MRO," not "my literal parent" — this only becomes visible once a class has more than one direct base.
- **Encapsulation** in Python is convention-based: `_name` signals "internal use" without any enforcement, and `__name` triggers name mangling (`_ClassName__name`) mainly to avoid attribute collisions — not to provide real security.
- Skipping `super().__init__()` in a subclass means the superclass's attributes never get set, surfacing later as an `AttributeError` the moment code tries to use them.
- Favor composition and shallow hierarchies over deep inheritance chains — reserve multiple inheritance for narrow, focused mixins.

Coming next: special methods and dataclasses — controlling how your objects are printed and compared, and cutting down the boilerplate needed to define them (Unit 4.3 — Special Methods & Dataclasses).

---

## 7. Reference Links

- [Python 3 Documentation — Inheritance (Tutorial)](https://docs.python.org/3/tutorial/classes.html#inheritance)
- [Python 3 Documentation — `super()` built-in function](https://docs.python.org/3/library/functions.html#super)
- [Python 3 Documentation — Multiple Inheritance](https://docs.python.org/3/tutorial/classes.html#multiple-inheritance)
- [Real Python — Supercharge Your Classes With Python `super()`](https://realpython.com/python-super/)
- [Real Python — Inheritance and Composition: A Python OOP Guide](https://realpython.com/inheritance-composition-python/)
- [W3Schools — Python Inheritance](https://www.w3schools.com/python/python_inheritance.asp)

[← Previous: 4.1 Object-Oriented Foundations](unit-4-1-object-oriented-foundations.md) | [Go back to TOC](../../README.md) | [Next: 4.3 Special Methods & Dataclasses →](unit-4-3-special-methods-dataclasses.md)

---

*© 2026 Revature · AI Native Engineering — Foundations · Unit 4.2 · Version 2.0*

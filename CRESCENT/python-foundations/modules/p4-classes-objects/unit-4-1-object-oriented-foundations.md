# Object-Oriented Foundations

---

## 1. Learning Objectives

By the end of this unit, you will be able to:

✓ Explain the difference between an object's **state** (its data) and its **behaviour** (what it can do).  
✓ Define a class using `__init__` and create multiple independent instances from it.  
✓ Distinguish between instance attributes (per-object) and class attributes (shared).  
✓ Write and call methods on an object, and explain exactly what `self` is and why Python needs it.  
✓ Spot the classic mutable-class-attribute trap, where "shared" data leaks between objects that were supposed to be independent.

---

## 2. Overview

Up to now, data and the functions that act on it have lived apart — a dictionary holding a student's marks in one place, a function that checks a pass/fail cutoff somewhere else entirely, with nothing tying them together except your own memory of which function goes with which data. **Object-oriented programming (OOP)** closes that gap: it bundles a piece of data together with the actions that belong to it, into a single unit called an **object**.

Think of a video game's character-creation screen. Before you play, you pick a character **class** — say, "Warrior." Warrior isn't a character you can actually control; it's a template describing what *every* warrior will have: a health bar, a level, an `attack()` move. The moment you click "Create Character," the game builds an actual warrior *from* that template — your warrior, with its own health, its own level, completely separate from anyone else's warrior even though they share the same class. That specific, playable warrior is an **object** (also called an **instance**), and the one-time act of creating it is called **instantiation**.

This unit is about that exact pattern in Python: writing a class that describes what an object will hold and what it can do, then creating real, independent objects from it.

You'll meet this pattern constantly later in this programme. A trained machine learning model you load in a later part is itself an object — its state is the millions of numbers it learned during training, and its behaviour is a `.predict()` method you call on new data. Same idea, bigger stakes.

---

## 3. Description

### 3.1 State and Behaviour — Thinking in Objects

- **State** — the data an object holds. On a game character, that's health, level, and inventory. On a `Student`, that's a name, a roll number, and a set of marks.
- **Behaviour** — the actions available on that data. A character can `attack()` or `heal()`. A `Student` might `calculate_average()` or check `has_passed()`.
- **Abstract data type** — a fancy-sounding term for a simple idea: describing something by *what it has* and *what it can do*, without caring yet how any of that is actually built underneath.

A warrior's state is its name, health, and level; its behaviour is `attack()` and `heal()`. That's exactly how you'd naturally describe a real warrior, or a real student — by their details, and by what they're capable of doing. OOP just gives that natural description a formal shape Python can run, which is what the rest of this unit builds in actual code.

### 3.2 Classes and Objects

- **Class** — the template. It defines what state and behaviour every object built from it will have, but a class by itself is not a usable object — "Warrior" the character class isn't a character you can move around the map.
- **Object (instance)** — one actual thing created from a class. Your specific warrior, with its own health total, is an instance of Warrior.
- **Instantiation** — the act of creating an object from a class — clicking "Create Character."

```python
class Student:
    pass

priya = Student()
rohan = Student()

print(type(priya))
print(priya is rohan)
```

Output:

```
<class '__main__.Student'>
False
```

`priya` and `rohan` are both built from the `Student` template, but `priya is rohan` prints `False` — they are two separate objects, the same way two warriors created from the same "Warrior" class are still two different characters standing in two different places.

### 3.3 The Constructor — `__init__`

- **`__init__`** — a special method Python runs automatically, every single time you create a new object. Short for "initialize." It's the game's "fill in your character's starting stats" screen, running the instant you click "Create Character."
- **Instance attributes** — data that belongs to one specific object, usually set inside `__init__`. Each warrior's own health total.
- **Class attributes** — data shared by *every* object of that class, written directly in the class body, outside any method. A game rule like "max level is 100," which is true for every warrior, not a stat any single warrior carries around individually.

```python
class Student:
    college = "Revature AI Native Engineering"   # class attribute — one shared value

    def __init__(self, name, roll_number, marks):
        self.name = name              # instance attribute — this student's own
        self.roll_number = roll_number
        self.marks = marks

priya = Student("Priya", 101, 78.5)
rohan = Student("Rohan", 102, 85.0)

print(priya.name, priya.marks)
print(rohan.name, rohan.marks)
print(priya.college, "==", rohan.college)
```

Output:

```
Priya 78.5
Rohan 85.0
Revature AI Native Engineering == Revature AI Native Engineering
```

`name` and `marks` differ per student — instance attributes. `college` is identical for both, because there is really only *one* `college` value, shared by reference from the class itself. Section 5's worked example shows exactly where that sharing becomes a trap instead of a convenience.

### 3.4 Methods and `self` — Why Python Needs It

- **Method** — a function written inside a class, meant to operate on one object's data.
- **`self`** — the first parameter of every method. It's how the method finds out *which* object it's currently running for.

Here's the part that actually confuses people, made concrete: when you write `priya.has_passed()`, Python is quietly rewriting that call behind the scenes into `Student.has_passed(priya)`. The object to the left of the dot doesn't vanish — it gets slipped in as the method's first argument, automatically, every time. `self` is just the parameter that catches it. Nothing magic is happening — it's the same as any other function argument, except Python fills this particular one in for you instead of asking you to.

```python
class Student:
    def __init__(self, name, marks):
        self.name = name
        self.marks = marks

    def has_passed(self):
        return self.marks >= 40

priya = Student("Priya", 78.5)
arjun = Student("Arjun", 32.0)

print(priya.has_passed())
print(arjun.has_passed())
```

Output:

```
True
False
```

`has_passed()` reads `self.marks` — and because `self` was filled in as `priya` for the first call and `arjun` for the second, the *same* method body correctly checks two completely different students' marks without ever getting them confused. That's the whole reason `self` exists: with two warriors on screen, `attack()` needs some way to know *which one* just swung its sword.

---

## 4. Real-World Application

Your college ERP's student profile page is one object doing exactly what §3.1 described: every field you see (name, attendance, marks) and every action available (recalculate GPA, mark attendance) live together on one `Student`-like object, instead of a dictionary and a separate function guessing at each other. That independence is also why two players in the same mobile game, both online right now, never interfere with each other — each is a separate instance of a `Player` class, so one player levelling up never touches the other player's level, exactly like `priya is rohan` printing `False` in §3.2.

A class attribute shows up anywhere one value is genuinely meant to be identical for everyone — an app's "Terms & Conditions version," for instance, is one shared value every user's account reads, the same role `college` played in §3.3, rather than a copy duplicated onto each account.

And `self` (§3.4) is exactly what lets a single `.predict()` method — which you'll call on a trained machine learning model later in this programme — work correctly no matter which model object you call it on. The method's code never changes; only which object's learned parameters it reads does.

---

## 5. Worked Example

**Goal:** Build a `Player` class for a simple game leaderboard, then run straight into the single most common early OOP bug — a class attribute that was supposed to be private to each player, but wasn't.

**1. Define a `Player` class — deliberately, with a bug.**

```python
class Player:
    inventory = []   # looks like a fresh empty list for each player... but isn't

    def __init__(self, name):
        self.name = name

    def pick_up(self, item):
        self.inventory.append(item)
```

**2. Create two players and give them different items.**

```python
alice = Player("Alice")
bob = Player("Bob")

alice.pick_up("Sword")
bob.pick_up("Shield")

print(alice.name, "has:", alice.inventory)
print(bob.name, "has:", bob.inventory)
```

Output:

```
Alice has: ['Sword', 'Shield']
Bob has: ['Sword', 'Shield']
```

**3. Spot the bug.** Bob picked up a Shield, not a Sword — yet Alice's inventory shows both items too. `inventory = []` was written in the class body, which makes it a **class attribute**: one single list, shared by every `Player` object, exactly like `college` was shared in §3.3. `self.inventory.append(...)` doesn't create a new list per player — it keeps reaching into that one shared list and changing it for everybody at once.

**4. Fix it by making `inventory` an instance attribute instead — created fresh, inside `__init__`.**

```python
class Player:
    def __init__(self, name):
        self.name = name
        self.inventory = []   # a brand-new list, created separately for THIS player

    def pick_up(self, item):
        self.inventory.append(item)

alice = Player("Alice")
bob = Player("Bob")

alice.pick_up("Sword")
bob.pick_up("Shield")

print(alice.name, "has:", alice.inventory)
print(bob.name, "has:", bob.inventory)
```

Output:

```
Alice has: ['Sword']
Bob has: ['Shield']
```

*Common mistake: reaching for a class attribute (`inventory = []` in the class body) when you actually wanted per-object data. The rule of thumb: if two different objects should ever be able to hold different values for it, it belongs inside `__init__` as an instance attribute — not in the class body.*

---

## 6. Summary

- **OOP** bundles data (state) and the actions on that data (behaviour) into one unit — an **object** — instead of keeping them separately linked only by convention.
- **A class** is a template; **an object (instance)** is one real, independent thing built from it. Two instances of the same class never share their instance attributes.
- **`__init__`** runs automatically on every new object and is where you set up instance attributes — data that belongs to that one object alone.
- **Class attributes**, written in the class body outside any method, are shared by *every* instance — useful for truly shared values, dangerous when a mutable one (like a list) is mistaken for private, per-object data.
- **`self`** is simply the object a method was called on, passed in automatically — `priya.has_passed()` runs as `Student.has_passed(priya)` behind the scenes, which is *why* the same method body works correctly for every different object.

Up next: inheritance and encapsulation — extending one class's behaviour into another, and controlling which parts of an object stay hidden from outside code.

---

*© 2026 Revature · AI Native Engineering — Foundations · Unit 4.1 · Version 1.0*

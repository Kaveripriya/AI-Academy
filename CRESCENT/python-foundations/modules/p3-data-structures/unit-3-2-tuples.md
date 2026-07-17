# Tuples

---

## 1. Learning Objectives

By the end of this unit, you will be able to:

✓ Create a tuple and access its elements by index.  
✓ Unpack a tuple into separate variables, including swapping two values.  
✓ Explain immutability and why it matters when choosing a data structure.  
✓ Work with nested tuples and basic tuple operations.  
✓ Decide when a tuple is the better choice over a list.

---

## 2. Overview

The previous unit gave you the list — flexible, ordered, and changeable. But not every collection of values should be changeable. A student's date of birth, the fixed (latitude, longitude) of a college campus, or the RGB values of a colour are not meant to change once created. Python's **tuple** is built for exactly this: an ordered collection that, once created, cannot be altered.

Think of a tuple like your permanent academic record — your roll number, the year you joined, and your date of birth are printed once on your ID card and are not meant to be edited afterwards. A list, by contrast, is more like your current semester's attendance sheet, which is updated constantly.

This unit covers creating and accessing tuples, unpacking them into separate variables, nesting tuples inside each other, immutability, and the handful of operations tuples support.

Tuples appear throughout Python itself — a function that needs to return more than one value almost always returns a tuple, and you will see this pattern again when working with model outputs in Part B.

---

## 3. Description

### 3.1 Creating and Accessing a Tuple

A tuple is written with comma-separated values, conventionally inside parentheses `( )`. This process of grouping values together is called **packing**.

```python
student = ("Priya", 21, "Computer Science")

print(student[0])
print(student[1])
```

Output:

```
Priya
21
```

A single-element tuple needs a trailing comma — `("Priya",)` is a tuple, but `("Priya")` is just a string in parentheses.

### 3.2 Tuple Assignment and Unpacking

**Unpacking** assigns each element of a tuple to its own variable in one line.

```python
name, age, branch = student
print(name)
print(branch)
```

Output:

```
Priya
Computer Science
```

The same mechanism gives Python its famously clean way to **swap two variables** without a temporary variable:

```python
a = 5
b = 10

a, b = b, a
print(a, b)
```

Output:

```
10 5
```

### 3.3 Nested Tuples

A tuple can contain other tuples, useful for representing coordinate-like or paired data.

```python
college_location = ("SRM Chennai", (12.8230, 80.0444))

print(college_location[1][0])
```

Output:

```
12.823
```

### 3.4 Immutability

Once a tuple is created, its elements **cannot** be changed, added, or removed.

```python
student[1] = 22
```

Output:

```
TypeError: 'tuple' object does not support item assignment
```

**When to prefer a tuple over a list:** use a tuple when the data represents a fixed record that should never change during the program (coordinates, a date of birth, RGB values) — the immutability itself acts as a safeguard against accidental edits elsewhere in your code.

### 3.5 Basic Tuple Operations

| **Operation** | **Example** | **Result** |
|---|---|---|
| Concatenation | `(1, 2) + (3, 4)` | `(1, 2, 3, 4)` |
| Repetition | `("a",) * 3` | `('a', 'a', 'a')` |
| Membership | `21 in student` | `True` |

---

## 4. Real-World Application

| **Where you see it** | **How Python is working behind the scenes** |
|---|---|
| **Your college ID card details** (roll number, date of birth, blood group) | These fixed, unchanging fields are exactly the kind of data a tuple is designed to hold. |
| **A location pin on a maps app** | Latitude and longitude are almost always stored and passed around as an immutable `(lat, lng)` tuple. |
| **A function returning "success and message"** from a payment app | Many backend functions return a tuple like `(status, message)` so both values travel together. |
| **RGB colour values in a design or UI tool** | A colour like `(255, 0, 0)` for red is a tuple — three fixed numbers that together define one unchanging value. |

---

## 5. Worked Example

**Scenario:** Your professor asks you to write a small script that stores a student's fixed record (name, roll number, date of birth) and safely unpacks it for a report.

**1. Create the record as a tuple.**
```python
student_record = ("Rohan Verma", "21CS045", "14-03-2005")
```

**2. Unpack it into separate variables.**
```python
name, roll_number, dob = student_record
print("Name:", name)
print("Roll Number:", roll_number)
print("Date of Birth:", dob)
```

Output:

```
Name: Rohan Verma
Roll Number: 21CS045
Date of Birth: 14-03-2005
```

**3. Attempt to correct a typo in the roll number — and see why a tuple stops you.**
```python
student_record[1] = "21CS046"
```

Output:

```
TypeError: 'tuple' object does not support item assignment
```

**4. Fix it correctly by creating a new tuple instead.**
```python
student_record = (name, "21CS046", dob)
print(student_record)
```

Output:

```
('Rohan Verma', '21CS046', '14-03-2005')
```

*Common mistake: trying to "edit" a tuple the way you would a list. A tuple can never be changed in place — the only fix is to build a brand-new tuple with the corrected values.*

---

## 6. Summary

- **Tuples** are ordered collections written inside `( )`, created through packing and read through unpacking.
- **Unpacking** assigns each element to its own variable in one line, and enables Python's clean `a, b = b, a` swap.
- **Immutability** means a tuple's contents can never be changed after creation — attempting it raises a `TypeError`.
- **Nested tuples** represent paired or coordinate-like data, such as a location's latitude and longitude.
- **Choose a tuple over a list** whenever the data is a fixed record that should never change during the program.

The next unit introduces sets — a collection built around one guarantee a list and tuple don't give you: uniqueness.

---

*© 2026 Revature · AI Native Engineering — Foundations · Unit 3.2 · Version 1.0*

# File Handling

---

## 1. Learning Objectives

By the end of this unit, you will be able to:

✓ Explain the difference between a text file and a binary file, and between an absolute and a relative path.  
✓ Open a file in the right mode, and explain concretely what goes wrong if you forget to close it.  
✓ Use a `with` block so a file closes automatically — even when an error happens partway through.  
✓ Read a CSV file into rows using the `csv` module.  
✓ Load and save data as JSON using `json.load()` and `json.dump()`.

---

## 2. Overview

Every program you've written so far forgets everything the moment it stops — variables live in memory, and memory is wiped when the program exits, as unit 1.1 showed you directly with a runtime restart. That's fine for a calculator, but useless for a program that has to remember yesterday's sales, a saved game, or a student's marks from last semester. To remember things between runs, a program reads and writes **files** — data stored on disk that survives after the program ends.

Working with a file always follows the same shape: you `open()` it, you read from or write to it, and you `close()` it when you're done — which is what actually guarantees your changes are saved and frees the file for anything else to use. This unit covers that open → use → close sequence correctly, and two of the most common shapes data comes in once it's stored: CSV and JSON.

---

## 3. Description

### 3.1 Text Files, Binary Files, and Paths

A **text file** stores human-readable characters — letters, digits, punctuation — encoded as bytes using a scheme like UTF-8: `.txt` notes, `.csv`, and `.json` are all text files. A **binary file** stores raw bytes that aren't meant to be read as characters — images (`.jpg`), audio (`.mp3`), compiled programs. Open a binary file in a text editor and you get scrambled-looking symbols, because the editor is trying to interpret non-text data *as* text. This unit works entirely in text mode, which is what CSV and JSON need.

A **path** is a file's address, and comes in two forms:

- **Absolute path** — the full location from the drive's root, e.g. `C:\Users\Priya\data\marks.csv`. It works no matter where your program runs from.
- **Relative path** — a location relative to wherever your program is currently running, e.g. `data/marks.csv`. Shorter, and portable if you move the whole project folder to a different computer — an absolute path breaks the moment the folder moves.

An absolute path is a full postal address — country, city, street, number. A relative path is "two doors down from where you're standing" — only meaningful once you know where you're standing. For most projects, relative paths are preferred for exactly that portability.

### 3.2 Opening, Using, and Closing a File

`open()` is Python's function for accessing a file: give it a path and a **mode** — what you intend to do once it's open — and it hands back a file object you read from or write to.

| **Mode** | **Meaning** |
|---|---|
| `r` | Read an existing file. Fails with an error if the file doesn't exist. This is the default if you give no mode. |
| `w` | Write. Creates the file if missing; **erases everything already inside** if it exists. |
| `a` | Append. Adds new content after what's already there, without erasing it. |
| `r+` | Read and write the same file without erasing its existing content. |

A quick way to remember the destructive ones: `"w"` wipes, `"a"` adds.

```python
file = open("notes.txt", "w")
file.write("Python file handling notes\n")
file.close()
```

Once open, the operations you'll use most:

- **`read()`** — the entire file, as one string.
- **`readline()`** — just the next line.
- **`readlines()`** — every line, as a list of strings.
- **`write()`** — writes a string into the file. It does **not** add a newline for you; include `\n` yourself if you want one.
- **`close()`** — flushes buffered data to disk and releases the file.

There's a fifth option worth knowing as the memory-friendly default for most real reading: looping directly over the file itself. A file is iterable, so `for line in file:` hands you one line at a time without ever loading the whole thing into memory — the same lazy, one-at-a-time behavior you saw with generators:

```python
with open("notes.txt", "r") as file:
    for line in file:
        print(line.strip())    # strip() drops the trailing newline
```

**Here's what forgetting `close()` actually costs you** — it's not just tidiness. When you `write()`, Python often holds what you wrote in a temporary memory buffer rather than sending it to disk immediately, for speed. `close()` is also the signal that flushes that buffer to disk for good. Skip it, and:

```python
file = open("draft.txt", "w")
file.write("Meeting notes for Monday")
# no file.close() here

with open("draft.txt", "r") as check:
    print(repr(check.read()))
```

Output:

```
''
```

The write genuinely happened — in memory — but without `close()`, there's no guarantee it ever reached disk. A second program (or a second `open()`, like the check above) reading that same file may see nothing at all, or only part of what you wrote.

### 3.3 The `with` Statement — Closing Automatically

Calling `close()` yourself has a sharper failure mode than just forgetting it: if an *error* happens between `open()` and `close()`, the `close()` line never runs at all, because Python stops executing that function the instant the error is raised.

```python
file = open("log.txt", "w")
file.write("step 1 complete\n")
result = 10 / 0          # crashes here
file.close()              # never reached
```

Python's **context manager**, written with `with`, fixes this at the language level — think of it as a self-closing door: you walk through it, and no matter how you leave the room, calmly or in a panic, the door swings shut behind you. It closes the file the moment the indented block ends, whether that block finished normally *or* crashed with an error.

```python
with open("log.txt", "w") as file:
    file.write("step 1 complete\n")
    result = 10 / 0       # still crashes...
# ...but the file is already closed by the time the crash propagates
```

```python
with open("notes.txt", "r") as file:
    content = file.read()
    print(content)
```

Output:

```
Python file handling notes
```

Notice there's no `file.close()` anywhere — `with` handles it, guaranteed. Use `with` for every file you open from this point on; treat manual `open()`/`close()` as something you now know exists but shouldn't reach for.

### 3.4 Reading CSV Files

A **CSV** (Comma-Separated Values) file stores rows and columns as plain text, comma-separated — the shape most spreadsheet exports come in. You *could* split each line on commas yourself, but that breaks the moment a value contains a comma of its own (like `"Smith, Jr."`). Python's built-in `csv` module handles that quoting for you and reads each row back as a list of strings.

```python
import csv

with open("marks.csv", "r", newline="") as file:
    reader = csv.reader(file)
    for row in reader:
        print(row)
```

Output:

```
['Name', 'Marks']
['Priya', '78']
['Rohan', '85']
```

Every value comes back as a **string**, even `'78'` — CSV has no concept of numbers, only text. If you need `78` the number, you convert it yourself with `int()`, which the worked example below does.

Always pass `newline=""` to `open()` when reading or writing CSV — the `csv` module's own documentation calls for it, so the module can manage line endings itself instead of rows occasionally splitting incorrectly on some platforms.

Two siblings worth knowing exist: `csv.writer` (with a `.writerow()` method) writes rows back out, and `csv.DictReader` gives you each row as a dictionary keyed by the header names instead of a plain list — handy when you'd rather write `row["Marks"]` than remember that marks is `row[1]`.

### 3.5 Working with JSON

**JSON** (JavaScript Object Notation) stores data as key-value pairs — structurally close to a Python dictionary — and it's the format almost every web API, including AI model APIs, sends and receives data in. It maps neatly onto Python types: a JSON object becomes a `dict`, a JSON array becomes a `list`, and strings, numbers, `true`/`false`, and `null` become `str`, `int`/`float`, `bool`, and `None`.

- **`json.dump(data, file)`** — writes a Python object out to a file as JSON. This is *serializing*.
- **`json.load(file)`** — reads JSON from a file back into a Python object. This is *deserializing*.

```python
import json

student = {"name": "Priya", "marks": 78}

with open("student.json", "w") as file:
    json.dump(student, file, indent=2)

with open("student.json", "r") as file:
    data = json.load(file)
    print(data)
```

Output:

```
{'name': 'Priya', 'marks': 78}
```

Unlike CSV, JSON *does* preserve types — `data["marks"]` comes back as the integer `78`, not the string `"78"`, because JSON has its own idea of numbers, strings, and booleans, and Python's `json` module maps them faithfully in both directions.

Two siblings work on **strings** in memory instead of files: `json.dumps(data)` returns a JSON string instead of writing one to a file, and `json.loads(text)` parses a JSON string instead of reading one. The rule of thumb: `load`/`dump` for files, `loads`/`dumps` (note the trailing `s`) for strings already sitting in a variable.

---

## 4. Real-World Application

Your semester result portal's "download as CSV" button, and a UPI app's "export transaction history" option, are both writing rows with `csv.writer` inside a `with` block on the server, so a slow download from a browser can't leave a file handle stuck open. Applications storing settings — API keys, feature flags, defaults — in a `config.json` loaded once at startup with `json.load()` is the same pattern from the opposite direction: a Python dictionary going out as JSON and coming back as one, which is also exactly what's happening on the wire every time your code calls an AI model's API.

---

## 5. Worked Example

**Goal:** You've been given `marks.csv` and need a program that prints each student's name with a Pass/Fail result — and you'll deliberately hit the header-row bug almost everyone hits the first time.

**1. The input file, `marks.csv`:**

```
Name,Marks
Priya,78
Rohan,32
Arjun,55
```

**2. First attempt — read every row, including the header, and try to grade it.**

```python
import csv

with open("marks.csv", "r", newline="") as file:
    reader = csv.reader(file)
    for row in reader:
        name, marks = row[0], int(row[1])
        result = "Pass" if marks >= 40 else "Fail"
        print(f"{name}: {result}")
```

Output:

```
ValueError: invalid literal for int() with base 10: 'Marks'
```

**3. Diagnose it.** The very first row read is `['Name', 'Marks']` — the header, not a student. `int("Marks")` has no way to become a number, because it isn't one. The `csv` module doesn't know some rows are headers and others aren't; it just hands you every row, in order, and trusts you to know your own file's shape.

**4. Fix it by consuming the header row once, before the loop starts:**

```python
with open("marks.csv", "r", newline="") as file:
    reader = csv.reader(file)
    header = next(reader)     # reads and discards row 1 — the header
    for row in reader:
        name, marks = row[0], int(row[1])
        result = "Pass" if marks >= 40 else "Fail"
        print(f"{name}: {result}")
```

Output:

```
Priya: Pass
Rohan: Fail
Arjun: Pass
```

`next(reader)` pulls exactly one row off the front and hands it to `header`, so the `for` loop that follows only ever sees the real data rows.

*Common mistake: forgetting the header exists at all until `int()` crashes on it. Whenever a CSV read blows up on the very first row, check whether that row is a header before you check anything else.*

---

## 6. Summary

- **Files** let data outlive the program that created it — unlike a Colab runtime's memory, which is wiped by every restart.
- **Modes** (`r`, `w`, `a`, `r+`) tell `open()` whether you intend to read, write (erasing existing content), append, or both.
- **Forgetting to close a file isn't just untidy** — writes can sit in a memory buffer and never actually reach disk until `close()` runs, so an unclosed file can look empty to anyone else reading it.
- **`with` closes a file automatically**, even if an error occurs inside the block — manual `open()`/`close()` has no such guarantee, since an error skips the `close()` line entirely.
- **`csv.reader()`** hands back every row — including the header — as a list of strings; **JSON**, via `json.load()`/`json.dump()`, preserves real types and is the format most APIs speak.

Now that data can survive on disk, the next unit covers what happens when that data — or anything else in your program — goes wrong: errors and exceptions, and how to handle them without crashing.

---

*© 2026 Revature · AI Native Engineering — Foundations · Unit 5.1 · Version 1.0*

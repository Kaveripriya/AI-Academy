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

Everything you've built so far has lived inside one Colab runtime — the moment that runtime restarts, every variable you created is gone, as unit 1.1 showed you the hard way. Real programs can't work like that. A marks portal needs last semester's marks to still exist when a student logs back in next month. Python's file-handling tools are how a program stores data somewhere that survives after the program itself has stopped running: a file on disk. Working with one always follows the same shape — you `open()` it, you read from or write to it, and you `close()` it when you're done, which is what actually saves your changes and frees the file for anything else to use.

Here's a real-world parallel: picture a bank locker. You go to the counter, and the clerk unlocks a specific numbered locker for you — that's `open()`. While it's unlocked, you can take documents out or put new ones in — that's reading and writing. When you're done, you hand it back and the clerk locks it — that's `close()`. Until it's locked again, that locker is *yours*: no one else can safely use it, and anything you just put inside isn't guaranteed safe yet. This unit is about doing that open → use → close sequence correctly, and about two of the most common shapes data comes in once it's stored: CSV and JSON.

---

## 3. Description

### 3.1 Text Files, Binary Files, and Paths

- **Text files** store human-readable characters — `.txt`, `.csv`, `.json`. Open one in Notepad and you can read it directly.
- **Binary files** store raw bytes that aren't meant to be read as text — `.jpg` images, `.mp3` audio. Open one in Notepad and you get scrambled-looking symbols, because Notepad is trying to interpret non-text data *as* text.
- **Absolute path** — the full location from the drive's root, e.g. `C:\Users\Priya\data\marks.csv`.
- **Relative path** — a location relative to wherever your program is currently running, e.g. `data/marks.csv`. Relative paths are shorter and, importantly, they still work if you move the whole project folder to a different computer — an absolute path breaks the moment the folder moves.

### 3.2 Opening, Using, and Closing a File

`open()` is Python's function for accessing a file: give it a path and a **mode** — what you intend to do once it's open — and it hands back a file object you can read from or write to. In the locker parallel from the Overview, this is the clerk handing you the locker:

| **Mode** | **Meaning** |
|---|---|
| `r` | Read an existing file. Fails with an error if the file doesn't exist — there's no locker to open. |
| `w` | Write a new file. Creates it if missing; **erases everything already inside** if it exists — like emptying the locker before you put anything new in. |
| `a` | Append. Adds new content after what's already there, without erasing it. |
| `r+` | Read and write the same file without erasing its existing content. |

```python
file = open("notes.txt", "w")
file.write("Python file handling notes\n")
file.close()
```

Once open, the operations you'll use most:

- **`read()`** — the entire file, as one string.
- **`readline()`** — just the next line.
- **`readlines()`** — every line, as a list of strings.
- **`write()`** — writes a string into the file.
- **`close()`** — hands the locker back.

There's a fifth option worth knowing as the default for most real reading: looping directly over the file itself. A file is iterable, so `for line in file:` hands you one line at a time without ever loading the whole thing into memory — the same lazy, one-at-a-time behavior you saw with generators:

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

The write genuinely happened — in memory — but without `close()`, there's no guarantee it ever reached the disk. A second program (or a second `open()`, like the check above) reading that same file may see nothing at all, or only part of what you wrote. The locker was never locked, so as far as anyone else is concerned, it might as well be empty.

### 3.3 The `with` Statement — Closing Automatically

Calling `close()` yourself has a sharper failure mode than just forgetting it: if an *error* happens between `open()` and `close()`, the `close()` line never runs at all, because Python stops executing that function the instant the error is raised.

```python
file = open("log.txt", "w")
file.write("step 1 complete\n")
result = 10 / 0          # crashes here
file.close()              # never reached
```

The locker stays open forever — nobody handed it back. Python's **context manager**, written with `with`, fixes this at the language level: it closes the file the moment the indented block ends, whether that block finished normally *or* crashed with an error.

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

A **CSV** (Comma-Separated Values) file stores rows and columns as plain text, comma-separated — the shape most spreadsheet exports come in. Python's built-in `csv` module reads each row back as a list of strings.

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

Always pass `newline=""` to `open()` when reading or writing CSV — without it, the `csv` module can't fully control line endings itself, and rows occasionally split incorrectly on some systems.

### 3.5 Working with JSON

**JSON** (JavaScript Object Notation) stores data as key-value pairs — structurally close to a Python dictionary — and it's the format almost every web API, including AI model APIs, sends and receives data in.

- **`json.dump(data, file)`** — writes a Python dictionary out to a file as JSON.
- **`json.load(file)`** — reads JSON from a file back into a Python dictionary.

```python
import json

student = {"name": "Priya", "marks": 78}

with open("student.json", "w") as file:
    json.dump(student, file)

with open("student.json", "r") as file:
    data = json.load(file)
    print(data)
```

Output:

```
{'name': 'Priya', 'marks': 78}
```

Unlike CSV, JSON *does* preserve types — `data["marks"]` comes back as the integer `78`, not the string `"78"`, because JSON has its own idea of numbers, strings, and booleans, and Python's `json` module maps them faithfully in both directions.

---

## 4. Real-World Application

Your semester result portal's "download as CSV" button, and a UPI app's "export transaction history" option, are both doing exactly §3.2/§3.4's pattern on the server: writing rows with `csv.writer` inside a `with` block, so a slow download from a browser can't leave a file handle stuck open — one row per record, plain text, no surprises.

JSON covers the other common case. A mobile app saving your profile settings so they're still there next time you open it is a dictionary written out with `json.dump()` and loaded back with `json.load()` (§3.5). That exact exchange — a Python dictionary going out as JSON and coming back as one — is also what's happening on the wire every time your code calls an AI model's API, which is why those two functions are among the most-used once you start working with real APIs later in this program.

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

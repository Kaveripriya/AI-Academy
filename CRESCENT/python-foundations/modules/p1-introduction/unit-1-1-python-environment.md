# The Python Environment

---

## 1. Learning Objectives

By the end of this unit, you will be able to:

✓ Explain why Python became the go-to language for AI work.
✓ Explain what "interpreted language" means, and what the REPL is.
✓ Use Google Colab confidently — cells, run order, and restarting a runtime.
✓ Write and run your first `print()` statements, and read their output correctly.

---

## 2. Overview

Before you can build anything with AI, you need a place to write instructions for the computer and a way to run them. In this program that place is **Python** — a programming language, meaning a set of words and rules for writing instructions a computer can follow — and the tool you run it in is **Google Colab**. Think of Python as the language you speak to the computer, and Colab as the room where that conversation happens.

We're keeping the scope small on purpose: what Python is, how it runs your code, where you'll write it, and how to run your first line. By the end, you'll have run a real program and watched it print a message back to you.

---

## 3. Description

### 3.1 Why Python for AI Work

If you look at any major AI tool — ChatGPT, PyTorch, TensorFlow — Python is almost always the language wrapped around it. Two reasons stand out:

- **It reads like plain English.** No semicolons, no curly braces, no declaring types before you use them. Python has less **syntax overhead** — fewer confusing symbols standing between your idea and the code that expresses it.
- **The richest ecosystem for AI.** An *ecosystem*, here, just means the large collection of ready-made code packages — for math, data, and training models — that other people have already built and you can reuse: NumPy, pandas, PyTorch, Hugging Face. Because almost every AI tool is written for Python first, choosing Python means that whole world works *with* you.

In an AI-native workflow — one where you build and use AI systems every day — Python is the layer where you connect the pieces: loading data, calling a model, reading back results. It's the glue, not necessarily the fastest part underneath. When PyTorch does heavy math, the actual number-crunching runs in pre-built C++ code; your Python line just says *what* should happen and *in what order*. That's why "Python is slow" and "Python runs today's AI models" aren't a contradiction — you're rarely looking at the part that's slow.

### 3.2 The Interpreter and Interactive Mode

Some languages (like C or Java) are **compiled**: translated all at once, ahead of time, into a separate file the machine runs later. If there's a typo near the end, you find out only after the whole translation finishes.

Python is **interpreted** — it reads and runs your code one line at a time, immediately, the way a live interpreter at a conference translates each sentence as the speaker says it.

| | Compiled (C, Java) | Interpreted (Python) |
|---|---|---|
| Runs after... | The entire program is translated first | Each line, as soon as it's reached |
| Feels like... | Write → wait → run | Write → run → see result immediately |

That immediate back-and-forth has a name: **interactive mode**, or the **REPL** — Read, Evaluate, Print, Loop. Type `3 + 4` and it prints `7` without you calling `print()` at all — the REPL just shows you the result of the last thing you typed:

```python
x = 5     # an assignment — shows nothing
x         # just the variable name on its own line — this DOES show
```

Output: `5`. Don't panic if a line shows nothing — assigning a value is a silent action, not a broken one. Colab gives you this same read-run-see rhythm, one cell at a time.

### 3.3 Google Colab as the Standard Environment

Google Colab is a free notebook that runs in your browser — but the computer running your code isn't your laptop, it's a machine in Google's data center you're borrowing for the session. It's a **rented** computer, and rentals can be taken back.

- **Cells.** A notebook is a stack of cells. You choose which cell to run and when — Colab does not auto-run anything.
- **Run order matters.** If Cell 2 uses a variable from Cell 1, you must run Cell 1 *first*. Skipping around is the #1 cause of confusing early errors.
- **The runtime can disconnect.** Step away for ~90 minutes and Colab reclaims the machine. Your notebook's *text* is safe; every variable in memory is gone.
- **Restarting the runtime.** Runtime → Restart runtime wipes memory and gives you a fresh start — your code stays, but every cell must run again from the top.
- **Save to Drive.** File → Save a copy in Drive. Colab autosaves your text periodically, but never your memory.

When something feels "stuck" for no obvious reason, restart the runtime and rerun from the top rather than debugging around it — a restart is cheap.

### 3.4 Running Your First Program — `print()`

`print()` is a **function** — a named, reusable instruction you use by writing its name followed by parentheses — and it's the first one every Python programmer reaches for. Whatever you put inside the parentheses gets displayed as output. Text you want printed exactly as written goes inside quotation marks, and is called a **string**.

```python
print("Hello, World!")
print("I am learning Python for AI.")
```

Output:

```
Hello, World!
I am learning Python for AI.
```

`#` starts a comment — Python ignores everything after it on that line. When you run several cells in sequence, each one's output appears below its own cell, in the order *you* ran them — reading that output, and checking it matches what you expected, is a core habit from day one.

---

## 4. Real-World Application

**A ChatGPT-style reply streaming back word by word.** Python orchestrates the request and response, but the actual matrix math generating each word runs in compiled code underneath — the "glue, not engine" split from §3.1, happening live.

**A Colab notebook suddenly saying "session expired" after you step away for lunch.** Nothing is broken — the rented machine behind your notebook was reclaimed after sitting idle, exactly as §3.3 described. Reconnecting hands you a fresh runtime with cleared memory.

---

## 5. Worked Example

**Goal:** Run cells in order, then see what happens when that order breaks.

**1. Create the notebook.** Go to colab.research.google.com, sign in, click **New Notebook**.

**2. Cell 1 — create a variable, and run it.**

```python
name = "Priya Nair"
```

No output appears — that's expected, assignment is silent.

**3. Cell 2 — use it, and run it.**

```python
print("Hello,", name)
```

Output: `Hello, Priya Nair`

**4. Restart the runtime** (Runtime → Restart runtime → confirm), then **re-run only Cell 2**, skipping Cell 1.

Output:

```
NameError: name 'name' is not defined
```

This isn't a bug in your code — the restart wiped the runtime's memory, so `name` no longer exists anywhere. Run Cell 1 again, then Cell 2, and it works.

**5. Save your notebook.** File → Save a copy in Drive.

*Common mistake: seeing a `NameError` and assuming your code is wrong. First check — did I actually run every cell it depends on, in order, since the last restart?*

---

## 6. Summary

- Python is popular for AI because it's easy to read, has a huge library ecosystem, and acts as the glue directing fast, compiled code that does the actual heavy computation.
- Python is **interpreted** — it runs your code one line at a time and gives you feedback immediately.
- The **REPL** (Read, Evaluate, Print, Loop) is why a bare variable name shows its value automatically, while an assignment shows nothing.
- **Google Colab** runs your code on a borrowed cloud machine — cells run in the order *you* run them, and a restart wipes memory but keeps your code.
- `print()` displays output to the screen; `#` marks a comment Python never runs.

Next up: variables, identifiers, and how Python handles different types of data.

---

*© 2026 Revature · AI Native Engineering — Foundations · Unit 1.1 · Version 1.0*

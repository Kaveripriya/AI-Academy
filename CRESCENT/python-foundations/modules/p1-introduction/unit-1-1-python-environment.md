# The Python Environment

---

## 1. Learning Objectives

By the end of this unit, you will be able to:

✓ Explain why Python is the common language for AI and machine learning work.
✓ Describe what an interpreter does and what "interactive mode" (the REPL) means.
✓ Open Google Colab, create a cell, run it, and read its output.
✓ Restart the Colab runtime and save a notebook to Google Drive.
✓ Write and run your first program using the `print()` function.

---

## 2. Overview

This is the very first topic in the program, and it assumes you have never written a line of code — that's exactly the right place to start. Before you can build anything with AI, you need a place to write instructions for the computer and a way to run them. In this program that place is **Python** (a programming language) and the tool you run it in is **Google Colab**. Think of Python as the language you speak to the computer, and Colab as the room where the conversation happens.

We keep the scope small on purpose: what Python is, how it runs your code, where you'll write it, and how to run your first line. By the end you will have run a real program and seen it print a message back to you.

---

## 3. Description

### 3.1 Why Python for AI Work

A **programming language** is a set of words and rules for writing instructions a computer can follow. There are many such languages. For artificial intelligence (AI) and machine learning (ML) work, Python is the one almost everyone uses.

Two reasons stand out. First, Python is **readable**: its code looks close to plain English and has low "syntax overhead," meaning you write fewer confusing symbols to get something done. That lets you focus on the idea instead of fighting the language. Second, Python has the richest **ecosystem** for AI. An ecosystem here means the large collection of ready-made, shareable code packages — for math, data, and training models — that other people have already built and that you can reuse. Because almost every AI tool is written for Python first, choosing Python means the whole AI world works with you rather than against you.

In an AI-native workflow — one where you build and use AI systems every day — Python is the layer where you connect the pieces: loading data, calling models, and reading results. It is the glue and the workbench at the same time.

### 3.2 The Interpreter and Interactive Mode

When you write Python, something has to turn your instructions into actions. That something is the **Python interpreter**: a program that reads your code and carries it out.

Languages generally run in one of two ways. A **compiled** language is translated all at once, ahead of time, into a separate file the machine runs later. An **interpreted** language like Python is read and executed directly, line by line, as it runs. You do not compile Python first; the interpreter just does what each line says, in order. The practical benefit for a beginner is speed of feedback — you write a line, run it, and see the result immediately.

That immediate back-and-forth has a name: **interactive mode**, also called the **REPL**. REPL stands for Read-Eval-Print Loop: the interpreter **R**eads what you type, **E**valuates (runs) it, **P**rints the result, and **L**oops back to wait for more. You type an instruction, you get an answer, you type the next one. Colab, which you'll meet next, gives you this same read-run-see rhythm one cell at a time.

### 3.3 Google Colab as the Standard Environment

**Google Colab** (short for Colaboratory) is a free tool from Google for writing and running Python in your web browser. It is a **hosted Jupyter notebook** environment, which means the code runs on Google's computers, not yours, and there is nothing to install or set up on your machine. You open a web page and you are ready to write Python.

A Colab document is called a **notebook**, and a notebook is made of **cells**. A cell is a box you type code into. You run one cell at a time, and each cell shows its output right underneath it. This gives you the same read-run-see loop as the REPL, but saved and organized on a page you can return to.

**Run order matters.** Cells do not run by themselves and they do not have to run top to bottom — they run when you tell them to, in the order you run them. Because of this, code in an earlier cell affects later cells only after you have actually run it. Running cells out of order is a common source of confusion, so a good habit is to run cells from top to bottom.

Behind your notebook is the **runtime**: the live Python session on Google's computer that remembers everything you have run so far in this session. Sometimes you want a clean slate — for example, if something gets into a confused state. You can **restart the runtime**, which wipes that memory and starts the Python session fresh; after a restart you must run your setup cells again. (Colab runtimes can also be given extra hardware such as a GPU for heavier AI work, but you do not need that yet.)

Your work is not lost when you close the tab: Colab **saves notebooks to Google Drive**, your Google storage, so you can reopen them later from any browser.

### 3.4 Running Your First Program

The `print()` function is how a program shows text to you. A **function** is a named, reusable instruction; you use it by writing its name followed by parentheses. Whatever you put inside the parentheses of `print()` gets displayed as output. Text you want printed literally goes inside quotation marks and is called a **string** — a piece of text data.

Here is your first program:

```python
print("Hello, world!")
```

Output:

```
Hello, world!
```

That is a complete program. You type it into a cell, run the cell, and the message appears underneath. When you run several cells in sequence, each one's output appears below its own cell, in the order you ran them. Reading that output — checking that what appeared matches what you expected — is a core part of programming from day one.

---

## 4. Real-World Application

Every AI workflow you build later in this program follows the same shape described above: Python is the layer that connects the pieces — loading data, calling a model, and reading back results. Even once you're calling a large, pre-built AI model instead of writing one from scratch, the code doing that connecting is still ordinary Python, run the same way you're running `print()` here.

---

## 5. Worked Example

**Goal:** Write and run your first program in Colab, then see what a runtime restart actually does.

**1. Open Colab and create a notebook.** Go to `colab.research.google.com` in your browser and sign in with a Google account. Create a new notebook (File → New notebook) — it saves to your Google Drive automatically.

**2. Type your first program into the empty code cell.**

```python
print("Hello, world!")
```

**3. Run the cell** — click the run button on its left, or press Shift+Enter. Read the output that appears directly below the cell.

Output:

```
Hello, world!
```

**4. In a second cell, print your name and a short sentence about why you're learning Python.** Run it, and confirm the message appears under its own cell, below the first one's output.

**5. Restart and rerun.** If you ever want a fresh start, use Runtime → Restart runtime, then run your cells again from the top.

*Common mistake: when something feels "stuck" or behaves strangely for no obvious reason, restart the runtime and rerun from the top rather than debugging around it — a restart is cheap, and it rebuilds a clean, predictable state.*

---

## 6. Summary

- Python is the standard language for AI work because it is readable and has the largest ecosystem of AI tools.
- Python is **interpreted**: the interpreter runs your code line by line, giving you fast feedback — the same read-run-see idea as the REPL.
- **Google Colab** runs Python in your browser with no setup; you write code in cells, and the runtime remembers what you have run in this session.
- Run order matters, restarting the runtime clears its memory, and notebooks save to Google Drive.
- `print()` displays output, and reading that output is how you confirm your program did what you meant.

Coming next: the building blocks of the language itself — storing values and doing math.

---

*© 2026 Revature · AI Native Engineering — Foundations · Unit 1.1 · Version 1.0*

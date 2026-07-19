# Portfolio & Diagnostic

---

## 1. Learning Objectives

By the end of this unit, you will be able to:

✓ Set up a personal portfolio repository that stays with you for the rest of the programme.  
✓ Structure that repo's folders so a stranger can predict where anything lives without opening every file.  
✓ Write a README that actually holds a reader's attention — title, quick start, and a look at what it produces.  
✓ Explain what the end-of-Part-A diagnostic checks, and why it exists at all.  
✓ Submit a diagnostic task that shows your Python and Git skills working together, and describe what faculty sign-off unlocks.

---

## 2. Overview

Every unit up to this point taught you one skill at a time. This unit doesn't teach a new one — it asks you to prove the ones you already have, in a form someone outside this course can actually see.

That form is a **portfolio repository**: one GitHub repository, created once, that you keep adding to for the rest of the programme. Think of it like a civil engineer's as-built drawings — a single, always-current record that shows exactly what's been built so far, updated after every phase of work rather than redrawn from scratch each time.

Attached to it is the **Part A diagnostic** — a small task that combines a Python skill with a Git skill, submitted through your portfolio repo. Passing it isn't a formality. It's the checkpoint that confirms you're ready for **Part B — AI Fundamentals**, where every module assumes you can already read/write Python comfortably and commit your work without being walked through it.

---

## 3. Description

### 3.1 The Portfolio Repository — and What Actually Makes a README Good

Unlike the small, disposable assignment repos from Unit 6.1, this repository is permanent. You create it once, today, and every later Part of the course adds to it — new scripts, new mini-projects, eventually a capstone.

Here's the part worth taking seriously: **almost nobody who looks at your portfolio — a recruiter, an instructor, a future teammate — reads your code first.** They open the README. If it's confusing, generic, or empty, most of them stop right there. The README isn't decoration; it's the only thing standing between "I built this" and someone actually believing it.

A README that does its job has four things, in this order:

- **Title and one-line description** — state plainly whose portfolio this is and what it collects. A reader should know what they're looking at from the first sentence, not by inferring it from folder names.
- **Quick start** — for each project worth highlighting, the exact command to run it, the one input it expects, and what it produces. This is what turns a passive reader into someone who actually tries the project.
- **Demo or example output** — show what the project actually produces (a sample summary line, a screenshot) so a reader can evaluate it without setting anything up themselves. For a portfolio, the README is often the *entire* interaction a visitor has with your work.
- **A short index of what's inside** — a portfolio holds several projects, not one. A brief list (name, one-line description, folder link) lets a reader choose what to look at instead of guessing.

Compare these two openings to the exact same README:

```
# My Portfolio
This is my portfolio for the AI course. I am learning Python.
```

```
# Priya's AI-Native Engineering Portfolio
Python projects and exercises from Parts 1–5 of the programme.

## Quick Start
Run the CSV summary tool: `python summary.py transactions.csv`
Prints row count, skipped/malformed rows, and basic stats.

## Example Output
Rows processed: 240, malformed rows skipped: 3
Average transaction: 842.50

## What's Inside
- `csv_summary_tool/` — reads and summarizes transaction CSVs
- `student_records/` — class-based student record manager
```

The first version is technically a README — it has words in the right file. It tells a reader nothing they couldn't guess from the repository's name. The second version, with the exact same amount of effort, tells a reader what to run, what they'll get back, and where to find more — before they've opened a single line of actual code. That's the difference between a portfolio that gets a second look and one that doesn't.

---

### 3.2 Repo and Folder Structure

A README earns a reader's trust in the first few seconds; the folder structure has to hold that trust once they start clicking around. Give the repo one folder per project, named for what's actually inside it — `csv_summary_tool`, not `week3` or `misc`. A reader should be able to predict where something lives from the folder name alone, the same way a clear commit message (from the last unit) tells them what changed without opening the diff.

Keep the layout flat. A handful of clearly named top-level folders beats several nested subfolders a visitor has to click through just to get oriented — and keep each project's files together rather than interleaved with unrelated work.

---

### 3.3 The End-of-Part-A Diagnostic

- **What it actually checks.** It's a small, self-contained task requiring one Python skill from Part A (reading and summarizing a file) *and* one Git skill (committing and pushing that result) — proof both foundations hold up together, not just individually in isolation.
- **How you submit it.** Commit the working script to your portfolio repository and push it, the same loop you practised in Unit 6.1.
- **What happens after.** Your instructor reviews the commit directly on GitHub. Sign-off on this specific, small task is what confirms your Part A foundation is solid enough for Part B — AI Fundamentals — to build on without re-teaching Python basics.

*Note: the diagnostic is small on purpose. It isn't testing anything new — every skill it needs has already been practised earlier in Part A. Its only job is confirming readiness.*

---

## 4. Real-World Application

A recruiter scanning a GitHub profile before an interview, or an internship platform like Internshala or LinkedIn asking for a GitHub link alongside a resume, is exactly the audience §3.1's specific-over-generic principle is written for — the README is almost always the first thing opened, and it's the difference between a profile that gets a follow-up question and one that doesn't. A portfolio repo isn't a nice-to-have anymore; it's table-stakes evidence next to a resume.

The same logic applies closer to home: when faculty check a project submission directly on GitHub instead of a zip file, commit history shows who actually did the work — §3.3's diagnostic is built on exactly that principle. A messy, unsorted repo creates the same problem at a bigger scale — a reader with no naming convention to rely on gives up faster than one who can predict the layout from a handful of well-named folders, which is exactly §3.2's point. And it doesn't stop after today — every later Part of this programme assumes you can commit and push without being walked through it, and this portfolio repo is where that ongoing work will actually live.

---

## 5. Worked Example

**Goal:** Turn a weak, generic README into one that actually says something specific about you, then complete and submit the diagnostic.

**1. Create the repository.** On GitHub, click **New repository**. Name it something like `yourname-ai-native-portfolio`. Set visibility to **Public** — a private repo can't be reviewed by an instructor or shown to a recruiter later. Click **Create repository**.

**2. Write a first draft, honestly.** Most first attempts look like this — and that's a normal starting point, not a failure:

```
# My Portfolio
This is my portfolio for the AI course. I am learning Python.
```

**3. Rewrite it to be specific**, using the four-part structure from §3.1:

```
# Priya's AI-Native Engineering Portfolio
Python projects and exercises from Parts 1–5 of the programme.

## Quick Start
Run the marks summary tool: `python summary.py marks.csv`
Prints the average, highest, and lowest mark, skipping malformed rows.

## Example Output
Rows processed: 42, malformed rows skipped: 1
Average: 74.2, Highest: 98, Lowest: 31

## What's Inside
- `marks_summary/` — reads and summarizes a CSV of student marks
```

Commit this with the message `Add portfolio README`.

**4. Create one folder per project**, named for its contents rather than a generic label — `marks_summary/`, not `week8` or `misc` — per §3.2. Move your strongest Parts 1–5 work into folders like this one at a time, committing as you go rather than dumping everything in one pass.

**5. Add the diagnostic script.** Inside a project folder, create `diagnostic.py` — a short script that reads a CSV of sample marks and prints a summary (average, highest, lowest), skipping or flagging any malformed row instead of crashing on it. This is deliberately a Part-A-level task: it exists to prove your Python and Git skills work together, not to be difficult.

**6. Commit and push it.** Use a message that says what the script does, not just that it exists: `Add Part A diagnostic — reads marks.csv and prints summary stats`.

**7. Notify your instructor.** Share the repository link as instructed, so they can review the commit and sign off.

*Common mistake: setting the repository to Private "until it's more finished." Nobody's portfolio is ever finished — it's meant to be watched growing. Keep it Public from day one, unless your instructor tells you otherwise.*

---

## 6. Summary

- **Your portfolio repository** is created once and grows for the entire programme — unlike the disposable per-assignment repos from Unit 6.1.
- **The README is read before the code, almost always** — title and description, a quick-start command, sample output, and an index of what's inside get a README taken seriously; a generic one gets skipped.
- **Folder structure carries the same weight** — one clearly named folder per project lets a stranger predict where anything lives without opening every file.
- **The Part A diagnostic** combines a Python skill and a Git skill in one small, deliberately easy task — its only purpose is confirming both foundations are solid.
- **Faculty sign-off** on that diagnostic is the actual gate into Part B — AI Fundamentals.

This closes Part A. Everything from here — variables and types, control flow, data structures, classes, files, and now version control — is the foundation Part B assumes is already comfortable, not something it re-teaches. Keep this portfolio repository open: the next thing you commit to it will be your first piece of actual AI work.

---

*© 2026 Revature · AI Native Engineering — Foundations · Unit 6.2 · Version 1.0*

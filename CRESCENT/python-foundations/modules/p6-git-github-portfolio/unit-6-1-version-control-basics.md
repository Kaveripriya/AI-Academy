# Version Control Basics

---

## 1. Learning Objectives

By the end of this unit, you will be able to:

✓ Explain what a version control problem actually is, and why Git solves it.  
✓ Tell Git and GitHub apart — what runs where, and what each one is actually for.  
✓ Describe the basic Git loop — repository, commit, push — and what each step does.  
✓ Accept an assignment through GitHub Classroom and locate your cohort repository.  
✓ Edit a file directly on the GitHub web interface, commit it with a clear message, and read a repository's commit history.  
✓ Explain why a README and a `.gitignore` file matter for a professional repository.

---

## 2. Overview

Here's a situation you've probably already lived through, just not with code: you're editing a document, you make a change, and it turns out to be wrong — but you already saved over the old version, so the working copy is gone. Every Python file you've written in this program so far has that exact risk. Save over it, and whatever was there before is simply gone.

**Git** is a tool that removes that risk. It doesn't stop you from editing files — it just keeps a permanent, labeled snapshot of your project every time you tell it to, so an old version is never actually gone, even after you've changed the file a hundred times since. Think of it like a video game's save-file system: you play (edit your code), and every so often you save your progress at a checkpoint (a **commit**) with a short note about what you just did. If a later attempt goes badly, you don't have to replay the whole game from scratch — you load an earlier save and you're right back where that checkpoint left you.

**GitHub** is a different thing, and mixing the two up is the single most common beginner confusion: Git is the save-file system itself, running on your own machine (or, in this course, inside your browser session); GitHub is a website that stores a copy of those save files online, so they survive even if your laptop dies, and so other people — your instructor, your teammates, a future employer — can actually see them. Git without GitHub still works fine, alone, on one computer. GitHub without Git wouldn't have anything to host.

This unit covers the basic Git loop, how your cohort's assignments are distributed and submitted through **GitHub Classroom**, how to do all of this through the GitHub website with no command line required, and the habits that separate a professional repository from a messy one. This is also the final content unit of Part A — everything you've built since Unit 1.1 comes together in the diagnostic in Unit 6.2, which gates your entry into Part B.

---

## 3. Description

### 3.1 The Basic Git Loop — Repository, Commit, Push

- **Repository ("repo").** A project folder that Git is keeping save-checkpoints for. Every file inside has its own complete history — you can always see what it looked like at any earlier checkpoint.
- **Commit.** One save-checkpoint: a snapshot of your files at this exact moment, stamped with a short message explaining what changed and why. A commit is never silently overwritten — even if you commit again five minutes later, the earlier commit is still sitting there in the history.
- **Push.** Sending your committed checkpoints from wherever you're working up to GitHub, so the online copy catches up to match what you have. In this unit, working through the GitHub website, committing and "pushing" collapse into a single click — there's no separate step, unlike when working from a local computer with the git command line (which you may encounter later in the program).

The point of all three together: at any moment, you can look back and answer "what did this project look like last Tuesday, and who changed what since then?" — a question that's simply unanswerable if you're just saving over the same file again and again.

One more property worth knowing: Git is **distributed**. Every copy of a repository — including the one on your machine, if you're working locally later in the program — carries the *entire* project history, not just a pointer back to GitHub. That's different from relying on a single shared drive with one master copy: there's no single point of failure, and you can inspect history even with no network connection at all.

---

### 3.2 GitHub Classroom and the Web Interface

- **GitHub Classroom** is how this cohort's assignments are distributed. Your instructor creates an assignment and shares a link; you **accept** it, and GitHub automatically creates your own personal repository for that assignment — a private copy that only you and your instructor can see.
- **No installation needed.** Everything in this unit happens directly in your browser on github.com — there's no separate program to install and no command-line syntax to memorize yet.
  - **Editing a file in the browser** — open the file, click the pencil (edit) icon, make your change, and commit it directly from the same page.
  - **Reading commit history** — every repository has a **Commits** tab listing every checkpoint ever saved for it, in order, with who saved it and when. It's the project's permanent diary, and you can click into any past commit to see exactly what that checkpoint changed.

*Note: accepting a GitHub Classroom assignment does not put you in a shared document that classmates are all editing at once — it's your own private repository, visible only to you and your instructor, the same way your own save file isn't mixed up with anyone else's.*

---

### 3.3 Good Habits for a Professional Repository

- **README as the front door.** `README.md` is the first file anyone sees when they open your repository. It should explain, in a few lines, what the project is and how to use it — a stranger landing on your repo with zero context should be able to read it and understand what they're looking at.
- **Write a commit message that means something later.** `"fixed stuff"` tells a reviewer — or you, in three weeks — nothing. `"Fix rounding error in average marks calculation"` tells them exactly what changed and why. The whole point of a commit message is to save someone (often future-you) from having to re-read the actual code just to figure out what a checkpoint was for.
- **Branches, briefly.** A **branch** is a separate line of save-checkpoints that doesn't touch the main project until you decide it's ready — `main` is the stable, always-working version; a **feature branch** is a sandboxed copy where you try something risky without putting the working version at risk. You'll work mostly on `main` in this unit; branching in depth comes later in the program.
- **`.gitignore`, briefly.** A `.gitignore` file lists files Git should never track or save checkpoints of — most importantly, files holding passwords or API keys, which should never end up in a shared, permanent, publicly-viewable history.

---

## 4. Real-World Application

Every professional engineering team runs on exactly the commit-history mechanism from §3.1 — a two-person startup and a massive open-source project like PyTorch are both, underneath, just a repository where every change is a permanent, attributable checkpoint. That's why a group project where one teammate's "fix" quietly breaks someone else's feature is usually solvable in minutes: open the Commits tab and see precisely which checkpoint introduced the regression and what it changed. It's also why recruiters looking at a GitHub profile treat commit history as evidence of real, dated work, not just a finished zip file with no story behind it.

The same logic is why platforms increasingly distribute and collect coursework the way §3.2 describes: GitHub Classroom hands each student a private repository and gets the finished result back the same way — no emailing zip files, and a real history behind the final submission.

---

## 5. Worked Example

**Goal:** Accept a real assignment repository, make two separate checkpoints in it, and use the commit history to prove — to yourself, not just your instructor — that both changes are permanently recorded.

**1. Accept the assignment.** Click the GitHub Classroom link your instructor shared. Sign in with your GitHub account, then click **Accept this assignment**. GitHub creates your own private repository and gives you a link to it.

**2. Open your repository and create a file.** Click **Add file → Create new file**. Name it `part-a-notes.md`.

**3. Write the first version, and commit it.**

```
# What I Learned in Part A

- Python syntax and control flow
```

Scroll to the commit box. Replace the default message with something specific: `Add initial Part A notes`. Click **Commit changes**. That's your first checkpoint.

**4. Edit the same file again, and commit a second, separate checkpoint.** Click the pencil icon on `part-a-notes.md`, and add two more lines:

```
# What I Learned in Part A

- Python syntax and control flow
- Data structures: lists, dictionaries, and sets
- Reading and writing files safely
```

Commit this with its own clear message: `Expand Part A notes with data structures and file handling`.

**5. Open the Commits tab and read the history back.** You should see two separate entries, each with your message, each timestamped. Click on the *first* commit — GitHub shows you the file exactly as it looked after that checkpoint, with only one bullet point, even though the file now has three. Nothing was overwritten; the old version is still there, permanently.

*Common mistake: leaving the default commit message (`Update part-a-notes.md`) instead of describing what actually changed. Two commits both named "Update part-a-notes.md" give you a history with two labeled save points and no way to tell, months later, which one mattered.*

---

## 6. Summary

- **Git solves a specific problem**: saving over a file destroys the old version, unless something is keeping labeled snapshots of it over time.
- **Git and GitHub are not the same thing** — Git is the save-checkpoint system itself; GitHub is the website that hosts those checkpoints online so they survive and can be shared.
- **The basic loop** — repository, commit, push — is the cycle you'll repeat every time you save and share work from here on; on the GitHub website, commit and push happen in the same click.
- **GitHub Classroom** distributes and collects assignments by giving each student their own private repository.
- **The web interface** lets you edit files, commit changes, and read commit history entirely in the browser — no command line required yet.
- **A clear README and specific commit messages** are what separate a professional repository from one nobody, including its own author, can make sense of later.

Up next: Unit 6.2, the portfolio diagnostic that closes out Part A.

---

*© 2026 Revature · AI Native Engineering — Foundations · Unit 6.1 · Version 1.0*

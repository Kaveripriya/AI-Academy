# 1.5 Strings — Indexing, Slicing & Methods

---

## What is a String?

You've been using **strings** since Unit 1.1 — text in quotes, like `"Hello, world!"`. This unit goes deeper: how to pull out parts of a string, how to transform it with built-in methods, and one important rule — strings can't be changed once created.

**Quick glossary:**

| Term | Meaning |
|---|---|
| **String** | Text data, in quotes |
| **Index** | The position of a character, starting at `0` |
| **Slice** | A section of a string, pulled out with `start:stop:step` |
| **String method** | A built-in action you call with `.`, e.g. `.upper()` |
| **Immutable** | Can't be changed after creation — a string stays as-is |
| **Escape character** | A `\` sequence that inserts something special, like a new line |

---

## Indexing — Getting One Character

Every character has a position, starting at `0`:

```python
name = "Python"
print(name[0])    # P
print(name[1])    # y
print(name[-1])   # n — negative counts from the end
```

`-1` is always the last character, `-2` the second-to-last, and so on — handy when you don't know the length in advance.

**Go past the end, and Python complains:**

```python
name[10]   # IndexError: string index out of range
```

---

## Slicing — Getting a Section

`start:stop:step` pulls out a chunk. **Stop is never included** — this trips up almost everyone at first.

```python
name = "Python"
print(name[0:3])   # Pyt   — index 0, 1, 2 (not 3)
print(name[2:])     # thon — from index 2 to the end
print(name[:4])     # Pyth — from the start to index 3
print(name[::2])    # Pto  — every 2nd character
print(name[::-1])   # nohtyP — reversed
```

This exact `start:stop:step` syntax works on lists too (coming up in Unit 3.1) — once it clicks here, it's already familiar there.

---

## Common String Methods

Methods are actions you call with a dot. They don't change the original string — they hand back a **new** one.

| Method | Does what | Example | Result |
|---|---|---|---|
| `.upper()` | ALL CAPS | `"hi".upper()` | `"HI"` |
| `.lower()` | all lowercase | `"HI".lower()` | `"hi"` |
| `.strip()` | removes leading/trailing spaces | `"  hi  ".strip()` | `"hi"` |
| `.replace(a, b)` | swaps text | `"hi".replace("h", "H")` | `"Hi"` |
| `.split(sep)` | splits into a list | `"a,b,c".split(",")` | `['a', 'b', 'c']` |
| `.join(list)` | joins a list into one string | `"-".join(['a','b'])` | `"a-b"` |
| `len(text)` | length of the string | `len("hi")` | `2` |

```python
raw_input = "  Ananya Roy  "
clean_name = raw_input.strip()
print(clean_name.upper())
```
```
ANANYA ROY
```

---

## Strings Are Immutable

Once created, a string **cannot be changed in place** — every method that "changes" it actually returns a brand-new string.

```python
name = "python"
name[0] = "P"   # TypeError — strings can't be edited like this
```

The fix is always **reassignment** — replace the whole variable with a new string:

```python
name = "python"
name = "P" + name[1:]
print(name)
```
```
Python
```

This is the opposite of what you'll see with lists in Unit 3.1, which *can* be changed in place — that contrast is exactly why strings come first.

---

## Escape Characters

A backslash `\` inside a string means "the next character is special":

| Escape | Means |
|---|---|
| `\n` | new line |
| `\t` | tab |
| `\"` | a literal quote mark, inside a double-quoted string |
| `\\` | a literal backslash |

```python
print("Line one\nLine two")
```
```
Line one
Line two
```

---

## Try it Yourself

```python
message = "  Welcome to Revature  "
```

**(a)** Clean it up and print in uppercase:

```python
print(message.strip().upper())
```
```
WELCOME TO REVATURE
```

**(b)** Print just the word `"Welcome"` using slicing (hint: the cleaned string starts with it).

**(c)** Split this into a list of words, then join them back together with a `-` instead of spaces:

```python
words = message.strip().split(" ")
print("-".join(words))
```
```
Welcome-to-Revature
```

**Your turn:** take your own name, print it reversed using `[::-1]`, then print it with every letter capitalized.

---

## Common Mistakes

- Forgetting that slicing's `stop` index is **excluded** — `name[0:3]` gives 3 characters, not 4
- Trying to edit a string directly (`name[0] = "P"`) — always reassign instead
- Assuming a method changes the original — it doesn't; you have to store the result
- Mixing up `\n` (new line) with typing an actual backslash — use `\\` if you mean a literal one
- Off-by-one errors with negative indexing — `-1` is the *last* character, not one past it

---

## Interview Questions

**Q: Are strings mutable or immutable in Python?**
A: Immutable. Once created, a string can't be changed — every "changing" method returns a new string instead of editing the original.

**Q: What does `name[2:5]` actually return?**
A: Characters at index 2, 3, and 4 — the `stop` index (5) is never included.

**Q: How do you reverse a string in Python?**
A: `name[::-1]` — an empty start and stop with a step of `-1` walks the string backward.

---

## Quick Recap

- Indexing gets one character; negative indices count from the end.
- Slicing (`start:stop:step`) gets a section — `stop` is always excluded.
- String methods (`.upper()`, `.strip()`, `.split()`, `.join()`, etc.) return a **new** string, never modify the original.
- Strings are immutable — to "change" one, reassign the variable.
- Escape characters (`\n`, `\t`, `\"`, `\\`) insert special characters inside a string.

Next up: **Unit 2.1 — Conditionals** — teaching your programs to make decisions with `if`, `elif`, and `else`.

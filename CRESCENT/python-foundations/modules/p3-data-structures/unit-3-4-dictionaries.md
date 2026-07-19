# Dictionaries

---

## 1. Learning Objectives

By the end of this unit, you will be able to:

✓ Create a dictionary and access a value by its key, and explain why keys must be unique and hashable while values can be anything.
✓ Add, modify, and delete entries with assignment, `del`, and `pop()`, and describe what each does when the key is missing.
✓ Loop over a dictionary's keys, values, and pairs using `keys()`, `values()`, and `items()`.
✓ Sort a dictionary's contents by key and by value using `sorted()` with a key function.
✓ Read and build nested dictionaries, and access a value two levels deep.
✓ Use `get()` and `setdefault()` to read or initialize a key safely without triggering a `KeyError`.
✓ Write a dictionary comprehension to build a dictionary from an iterable, and to invert an existing one.

---

## 2. Overview

Lists and sets both answer "which items do I have?" A dictionary answers a different, extremely common question: "given *this*, what is the *corresponding* thing?" Given a username, what is their email? Given a word, how many times did it appear? Each of these is a **lookup** — you hold a key and want the value attached to it.

You could fake a lookup with two parallel lists matched by position, but that's fragile and slow. A Python **dictionary** stores the pairing directly: a collection of **key-value pairs**, where you retrieve a **value** by giving its **key**, instead of by position — and every key has to be unique, so there's never any doubt about which value it points to. It's the single most-reached-for structure in real Python, because so much real data is naturally a set of labelled values: a user record, a config file, a JSON payload from an API, a count of how often each thing occurred.

This unit covers building and reading a dictionary, changing its entries, looping through it three different ways, sorting it, nesting one dictionary inside another, two methods that stop a missing key from crashing your program, and dictionary comprehensions.

---

## 3. Description

### 3.1 What a Dictionary Is

A **dictionary** (type `dict`) is a **mutable mapping of unique keys to values**. "Mapping" is the key word: it maps each key to exactly one value, the way a real-world dictionary maps each word to its definition. Four properties define it:

- **Key-value pairs.** Every entry is a pair — a key and the value it points to. You look things up *by key*, never by position.
- **Keys are unique.** A dictionary cannot hold the same key twice; assigning to an existing key overwrites its value rather than adding a second entry.
- **Keys must be hashable (immutable).** Strings, numbers, and tuples all work fine as keys; a list can't be one, because a key's hash is used to find its value fast, so the key must never change.
- **Values can be anything.** A value can be a number, a string, a list, or even another dictionary, and values need not be unique — two different keys may point to the same value.

A dictionary is written with curly braces, a colon between each key and value, commas between pairs:

```python
capitals = {"France": "Paris", "Japan": "Tokyo", "Egypt": "Cairo"}
```

It helps to place a dictionary next to the structures you already know:

| | List | Set | Dict |
|---|---|---|---|
| Access by | position `[i]` | (membership only) | **key `[k]`** |
| Stores | values | unique values | **key → value pairs** |
| Duplicates allowed | yes | no | no duplicate **keys** |
| Ordered | yes | no | **yes (insertion order)** |
| Written with | `[ ]` | `{ }` / `set()` | `{key: value}` / `{}` |

A dictionary **is ordered**: since Python 3.7 it remembers the order you inserted keys in, and looping visits them in that order — a genuine difference from the unordered set. And `{}` is an empty dict, `{key: value}` is a non-empty dict; `set()` is how you make an empty *set* instead.

### 3.2 Creating and Accessing by Key

There are three everyday ways to create a dictionary:

```python
capitals = {"France": "Paris", "Japan": "Tokyo"}   # 1. braces with literal pairs
person = dict(name="Ana", age=30)                   # 2. dict() with keyword arguments
pairs = [("a", 1), ("b", 2)]
d = dict(pairs)                                     # 3. from a list of (key, value) pairs
```

You access a value by putting its key in square brackets — the same brackets you used for list indexing, except the "index" is now a key instead of a position:

```python
print(capitals["France"])
```

Output:

```
Paris
```

The one thing to watch: asking for a key that doesn't exist raises `KeyError` and stops your program — the dictionary equivalent of an out-of-range list index:

```python
print(capitals["Spain"])
```

Output:

```
KeyError: 'Spain'
```

You can check for a key first with `in` — but note that `in` on a dictionary tests **keys, not values**:

```python
print("France" in capitals)
print("Paris" in capitals)
```

Output:

```
True
False
```

`"Paris"` is sitting right there as a value, but `in` never looks at values at all — only keys.

### 3.3 Adding, Modifying, and Deleting Entries

A dictionary is mutable, so you can change its contents after creation. Three operations cover almost everything.

**Add or modify with assignment.** `d[k] = v` is both add and update: if `k` is new, the pair is added; if `k` already exists, its value is overwritten. There's no separate "add" versus "update" syntax — a key can appear only once, so a second assignment can only overwrite:

```python
scores = {"ana": 10}
scores["ben"] = 8       # "ben" is new -> adds the pair
scores["ana"] = 12      # "ana" exists -> overwrites 10 with 12
print(scores)
```

Output:

```
{'ana': 12, 'ben': 8}
```

**Delete with `del`.** `del scores["ben"]` removes a key and its value entirely — and, like bracket access, raises `KeyError` if the key is absent.

**Remove-and-return with `pop()`.** `pop()` deletes a key *and* hands you back its value, which `del` never does:

```python
taken = scores.pop("ben")
print(taken)
```

Output:

```
8
```

`pop()` also raises `KeyError` on a missing key — unless you give it a default second argument, in which case it returns that default instead of raising: `scores.pop("zoe", 0)` returns `0` with no error. Reach for `del` when you only want the key gone; reach for `pop()` when you want the value on the way out.

### 3.4 Looping Through a Dictionary

Because a dictionary holds pairs, there are three things you might want to iterate — the keys, the values, or both together — and Python gives you a method for each:

```python
stock = {"pens": 120, "notebooks": 45, "erasers": 200}

for item in stock.keys():          # just the keys
    print(item)

for count in stock.values():       # just the values
    print(count)

for item, count in stock.items():  # both, paired together
    print(item, "->", count)
```

Output:

```
pens
notebooks
erasers
120
45
200
pens -> 120
notebooks -> 45
erasers -> 200
```

Writing `for k in d` (no `.keys()` at all) is identical to `for k in d.keys()` — keys are what you get by default. `items()` is the workhorse: it yields each pair as a `(key, value)` tuple you unpack into two loop variables, which is cleaner than looping the keys and re-looking-up each value with `d[k]`. One note: `keys()`, `values()`, and `items()` return **view objects**, not lists — a view stays connected to the dictionary, and you can wrap it in `list()` if you need an actual list.

### 3.5 Sorting a Dictionary — by Key and by Value

A dictionary keeps insertion order, but often you want it in *sorted* order. A dictionary itself has no built-in order to rearrange — what you actually sort is a *list of its items*, built fresh from it, using the `sorted()` you already use on lists.

**Sort by key.** Passing a dictionary straight to `sorted()` sorts its keys:

```python
scores = {"ben": 8, "ana": 12, "cara": 10}
for name in sorted(scores):
    print(name, scores[name])
```

Output:

```
ana 12
ben 8
cara 10
```

**Sort by value.** Sort the *pairs* from `items()` and tell `sorted()` to use the value with the `key=` argument and a small `lambda` — a one-line anonymous function:

```python
by_value = sorted(scores.items(), key=lambda kv: kv[1])
top = sorted(scores.items(), key=lambda kv: kv[1], reverse=True)
print(by_value)
print(top)
```

Output:

```
[('ben', 8), ('cara', 10), ('ana', 12)]
[('ana', 12), ('cara', 10), ('ben', 8)]
```

Read `key=lambda kv: kv[1]` as "for each pair `kv`, sort using `kv[1]`, the value"; `reverse=True` ranks highest first. Either result is a list of pairs you can loop over directly, or feed back into `dict(...)` to build a new, value-ordered dictionary — `sorted()` returns a new list and never changes the original dictionary.

### 3.6 Safe Access: `get()` and `setdefault()`

Looking up a key that might not exist, directly, risks a `KeyError` crashing your program. Two methods let you read or initialize a key without that risk.

**`get(key, default)` — read with a fallback.** Returns the value if the key exists, and otherwise returns a default instead of raising — `None` if you don't supply one:

```python
print(capitals.get("France"))
print(capitals.get("Spain", "unknown"))
```

Output:

```
Paris
unknown
```

`get()` never modifies the dictionary — a missing key stays missing; you just get a fallback value back.

**`setdefault(key, default)` — read, or insert then read.** If the key exists, it returns the existing value and changes nothing. If the key is absent, it *inserts* the key with the default *and* returns that default:

```python
prefs = {"theme": "dark"}
prefs.setdefault("theme", "light")   # exists -> returns "dark", no change
prefs.setdefault("font", "sans")     # absent -> inserts, returns "sans"
print(prefs)
```

Output:

```
{'theme': 'dark', 'font': 'sans'}
```

`setdefault()` shines when you're building a dictionary whose values are containers — for example, grouping items into lists under a key:

```python
by_first_letter = {}
for name in ["ana", "ben", "amy"]:
    by_first_letter.setdefault(name[0], []).append(name)
print(by_first_letter)
```

Output:

```
{'a': ['ana', 'amy'], 'b': ['ben']}
```

On the first `"a"`, `setdefault` inserts `"a": []` and returns that new empty list to `.append` onto; on the second `"a"`, it returns the *existing* list. This get-or-create-the-container move is a workhorse pattern for grouping.

### 3.7 Nested Dictionaries

Because a dictionary's values can be anything, a value can itself be a dictionary. A **nested dictionary** is the natural way to model records that have their own fields — the shape JSON from an API, a config file, or a database row typically arrives in:

```python
users = {
    "u1": {"name": "Ana", "age": 30, "city": "Paris"},
    "u2": {"name": "Ben", "age": 25, "city": "Tokyo"},
}
print(users["u1"]["name"])
```

Output:

```
Ana
```

To reach a value two levels deep, chain the bracket lookups: `users["u1"]` gives you Ana's whole record (the inner dict), and `["name"]` then pulls the name out of it. Nested records often have *missing* fields, so `get()` is especially valuable one level down, where a bare `record["city"]` would raise `KeyError` for any record lacking that field.

### 3.8 Dictionary Comprehension — Building and Inverting

Just like the list and set comprehensions from earlier units, a dictionary can be built in one line — with one addition: because a dictionary needs a key *and* a value, you write `key: value` before the `for` clause:

```python
squares = {n: n * n for n in range(1, 5)}
print(squares)
```

Output:

```
{1: 1, 2: 4, 3: 9, 4: 16}
```

**Inverting a dictionary** — swapping keys and values so you can look up in the opposite direction — is the signature two-liner comprehensions make trivial: swap which side of the colon each name sits on.

```python
inverted = {value: key for key, value in squares.items()}
print(inverted)
```

Output:

```
{1: 1, 4: 2, 9: 3, 16: 4}
```

Inverting only works cleanly when the original values are unique — if two keys had shared a value, one of them would silently overwrite the other during the flip, since dictionary keys can never repeat.

---

## 4. Real-World Application

Dictionaries are the most-used non-trivial structure in real Python, almost always for one of these jobs:

- **Records and configuration.** A single "thing with named fields" — a user, a product, a settings block — is a dictionary. Collections of such things become a dictionary of dictionaries keyed by id (§3.7). This is exactly the shape of JSON, so any data pulled from a web API or read from a config file lands in your program as nested dictionaries.
- **Lookup tables.** Any "translate this into that" mapping is a dictionary: country code to country name, product id to price, HTTP status code to message. Instead of a chain of `if`/`elif`, you store the mapping once and look it up, with `get()` supplying a fallback for inputs you didn't anticipate.
- **Counting and grouping.** "How many times did each thing occur?" is a dictionary built with the `get(x, 0) + 1` pattern; "group these items by attribute" is a dictionary built with `setdefault(key, []).append(item)`. Both are everywhere in analytics and log processing.
- **Reverse lookups.** When a name-to-id map suddenly needs id-to-name, you invert it with a one-line comprehension (§3.8) rather than maintaining two hand-synced dictionaries — as long as the values were unique.

---

## 5. Worked Example

**Goal:** Count how many times each word appears in a list, then report the words ranked most-frequent-first.

**1. Start with the raw data.**

```python
words = ["red", "blue", "red", "green", "red", "blue"]
```

**2. Count each word using `get()` as a safe "read-or-zero."**

```python
counts = {}
for w in words:
    counts[w] = counts.get(w, 0) + 1

print(counts)
```

Output:

```
{'red': 3, 'blue': 2, 'green': 1}
```

`counts.get(w, 0) + 1` reads the current count — or `0` if the word is new — adds one, and stores it back with assignment. The first sighting of a word initializes it; every later sighting increments it.

**3. See why a bare bracket lookup would have failed instead.**

```python
counts["yellow"] += 1
```

Output:

```
KeyError: 'yellow'
```

`"yellow"` was never seen, so the dictionary has no such key yet — `+=` needs to *read* the current value first, and there isn't one. This is exactly the crash `.get(w, 0)` was written to avoid in step 2.

**4. Rank the words by count, highest first.**

```python
ranked = sorted(counts.items(), key=lambda kv: kv[1], reverse=True)
print(ranked)
```

Output:

```
[('red', 3), ('blue', 2), ('green', 1)]
```

*Common mistake: writing `word_count[word] += 1` on the first-ever sighting of a word and expecting it to just work. A dictionary doesn't invent a starting value of `0` for a key it's never seen — that's exactly the gap `.get(word, 0)` fills before you add one.*

---

## 6. Summary

- A **dictionary** is a mutable, insertion-ordered mapping of unique, hashable keys to arbitrary values; you access a value by key with `d[k]`, and a missing key raises `KeyError`.
- Assignment `d[k] = v` **adds** a new key or **overwrites** an existing one; `del d[k]` removes a key; `d.pop(k[, default])` removes it and returns its value.
- Loop with **`keys()`, `values()`, or `items()`** — use `for k, v in d.items()` whenever you need both the key and its value.
- **Sort keys with `sorted(d)`**, sort by value with `sorted(d.items(), key=lambda kv: kv[1])` — sorting produces a new list and leaves the dictionary's own order intact.
- **`get(k, default)`** reads safely without raising or changing the dict; **`setdefault(k, default)`** returns the existing value or inserts the default — ideal for get-or-create-a-container patterns.
- A **nested dictionary** stores dictionaries as values; reach two levels deep with chained brackets. A **dictionary comprehension** builds — or, by swapping key and value, inverts — a dictionary in one line, cleanly only when the original values are unique.

Up next: iterators, generators, and the `collections` module — the machinery quietly running behind almost every `for` loop you've written so far.

---

*© 2026 Revature · AI Native Engineering — Foundations · Unit 3.4 · Version 1.0*

# Dictionaries

---

## 1. Learning Objectives

By the end of this unit, you will be able to:

✓ Create a dictionary and look up, add, change, and delete a key-value pair.  
✓ Loop through a dictionary's keys, values, and items — and know which one you actually need.  
✓ Sort a dictionary's contents by key or by value.  
✓ Build and read a nested dictionary.  
✓ Use `get()` and `setdefault()` to avoid crashing on a missing key.  
✓ Write a dictionary comprehension, including flipping a dictionary's keys and values.

---

## 2. Overview

Lists and tuples both make you look things up by position — `marks[2]` means "whatever is sitting in slot 2," and you'd have to already know that Arjun's mark happens to live there. That's fine for a short, ordered sequence. It falls apart the moment you want to ask a more natural question: "what's *Arjun's* mark," not "what's in slot 2."

A Python **dictionary** solves exactly that: it's a collection of **key-value pairs**, where you retrieve a **value** by giving its **key** directly, instead of by position — and every key has to be unique, so there's never any doubt about which value it points to.

Picture the coat-check counter at a large wedding hall. You hand over your coat, and the attendant gives you back a small numbered ticket — say, **47**. Later, you don't describe your coat to get it back ("it's the black one, third from the left"); you hand over ticket 47, and the attendant goes straight to hook 47 and returns your coat immediately, without checking any other hook. The ticket only works because no two coats ever share the same number — a second coat handed in under "47" would simply replace whatever was on that hook before. That's exactly the guarantee a dictionary's keys give you.

This unit covers building and reading a dictionary, changing its entries, looping through it three different ways, sorting it, nesting one dictionary inside another, two methods that stop a missing key from crashing your program, and dictionary comprehensions. You'll use this structure constantly from here on — a single record with named fields (a student, a product, an API response) is almost always a dictionary.

---

## 3. Description

### 3.1 Creating and Accessing a Dictionary

A dictionary is written with `key: value` pairs inside curly braces `{ }`, each key separated from its value by a colon.

```python
ticket_47 = {"item": "black overcoat", "owner": "Meera", "counter": 2}

print(ticket_47["item"])
```

Output:

```
black overcoat
```

`ticket_47["item"]` is the lookup — give it a key, get back its value, with nothing in between. Behind the scenes, Python doesn't scan every entry to find `"item"`; it uses a technique called **hashing** to jump almost straight to the right spot, the same way the attendant walks directly to hook 47 instead of checking every hook in the room. You don't need the mechanics of hashing yet — just know that this is *why* a dictionary lookup stays fast even with a million entries in it.

That same hashing trick is also why a key has a restriction a value never does: a key must be **hashable**, which in practice means immutable. Strings, numbers, and tuples all work fine as keys; a list can't be one, because Python needs a key to never change once it's stored.

Look up a key that doesn't exist, and Python doesn't guess — it tells you immediately:

```python
print(ticket_47["size"])
```

Output:

```
KeyError: 'size'
```

Checking first with `in` avoids that crash — but `in` on a dictionary only ever tests **keys**, never values: `"black overcoat" in ticket_47` is `False`, even though that text is sitting right there as a value, because `in` never looks at values at all.

### 3.2 Adding, Modifying, and Deleting Entries

```python
ticket_47["counter"] = 5          # modify — this key already existed
ticket_47["claimed"] = False      # add — this key is brand new
del ticket_47["owner"]            # delete — remove a key entirely

print(ticket_47)
```

Output:

```
{'item': 'black overcoat', 'counter': 5, 'claimed': False}
```

Notice the *same* syntax, `ticket_47[key] = value`, both adds a new entry and updates an existing one — Python decides which one you meant by checking whether that key is already there.

`pop("claimed")` also removes a key, but hands you the removed value back at the same time, which `del` never does:

```python
was_claimed = ticket_47.pop("claimed")
print(was_claimed)
```

Output:

```
False
```

### 3.3 Looping Through a Dictionary — Three Ways

A dictionary gives you three different things to loop over, and picking the wrong one is a common early mistake:

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

`for item in stock:` (no `.keys()` at all) loops over the keys too — `.keys()` is there for clarity, not because it's required. Reach for `.items()` whenever you need the key *and* its value together inside the loop, which is most of the time.

### 3.4 Sorting a Dictionary

A dictionary itself has no built-in order to rearrange — what you actually sort is a *list of its items*, built fresh from it:

```python
stock = {"pens": 120, "notebooks": 45, "erasers": 200}

by_name = dict(sorted(stock.items()))
by_count_desc = dict(sorted(stock.items(), key=lambda item: item[1], reverse=True))

print(by_name)
print(by_count_desc)
```

Output:

```
{'erasers': 200, 'notebooks': 45, 'pens': 120}
{'erasers': 200, 'pens': 120, 'notebooks': 45}
```

`sorted(stock.items())` sorts the `(key, value)` pairs by key first (alphabetically, here). The `key=lambda item: item[1]` in the second line tells `sorted()` to compare pairs by their *second* element — the value — instead; `reverse=True` flips it to highest first.

### 3.5 Nested Dictionaries

A value inside a dictionary can be another dictionary — this is how you represent one thing (a ticket) that itself has several named fields.

```python
counter = {
    47: {"item": "black overcoat", "owner": "Meera"},
    48: {"item": "red scarf", "owner": "Ishaan"},
}

print(counter[48]["owner"])
```

Output:

```
Ishaan
```

`counter[48]` gets you the inner dictionary `{"item": "red scarf", "owner": "Ishaan"}`; the second `["owner"]` reaches inside *that* dictionary for one field of it. Chain as many lookups as the nesting goes deep.

### 3.6 Avoiding a Crash: `get()` and `setdefault()`

Looking up a key that might not exist, directly, risks a `KeyError` crashing your program — you saw this in §3.1. `.get()` sidesteps that by handing back a default value instead of raising an error:

```python
print(counter.get(50, "no ticket issued with that number"))
```

Output:

```
no ticket issued with that number
```

`setdefault(key, default)` is a close relative, for a different job: it adds the key with that default *only if the key is missing*, and leaves it alone if the key already exists.

```python
counter.setdefault(47, {"item": "unknown", "owner": "unknown"})  # 47 exists — untouched
counter.setdefault(49, {"item": "blue umbrella", "owner": "Zara"})  # 49 is new — added

print(counter[47]["item"])
print(counter[49]["item"])
```

Output:

```
black overcoat
blue umbrella
```

### 3.7 Dictionary Comprehensions

Just like the list comprehensions from a previous unit, a dictionary can be built in one line:

```python
squares = {n: n * n for n in range(1, 5)}
print(squares)
```

Output:

```
{1: 1, 2: 4, 3: 9, 4: 16}
```

Swapping the position of the key and value expression flips the whole dictionary — every value becomes a key, and vice versa:

```python
inverted = {value: key for key, value in squares.items()}
print(inverted)
```

Output:

```
{1: 1, 4: 2, 9: 3, 16: 4}
```

Inverting only works cleanly when the original values are themselves unique — if two keys had shared a value, one of them would silently overwrite the other during the flip, since dictionary keys can never repeat.

---

## 4. Real-World Application

A parcel-tracking app looking up your delivery by tracking number is doing exactly §3.1's lookup — the tracking number is the key, and the app jumps straight to your parcel's status instead of scanning every parcel in the system.

A game that saves your profile as `{"level": 12, "gold": 340, "inventory": {...}}` is leaning on §3.5's nested dictionaries: your whole profile is one record, and `inventory` is itself a smaller record living inside it.

And two everyday behaviors come straight from §3.4 and §3.6: a leaderboard showing players ranked highest-score-first is built the same way as `by_count_desc` — the underlying data has no order until you sort a list of its items — while a contacts app calmly showing "No contact found" instead of crashing is `.get()` with a default, turning a missing key into a message instead of a `KeyError`.

---

## 5. Worked Example

**Goal:** Track a coat-check counter through a busy evening — issue tickets, look one up safely, handle a lost ticket, and print a report sorted by counter number.

**1. Start the counter's dictionary — a few tickets already issued.**

```python
counter = {
    47: {"item": "black overcoat", "counter_no": 2},
    48: {"item": "red scarf", "counter_no": 1},
    49: {"item": "blue umbrella", "counter_no": 2},
}
```

**2. A guest returns with ticket 48 — look it up.**

```python
ticket = 48
claim = counter.get(ticket, "No coat found for that ticket")
print(claim)
```

Output:

```
{'item': 'red scarf', 'counter_no': 1}
```

**3. A guest insists they have ticket 60 — but no such ticket was ever issued.**

```python
ticket = 60
claim = counter.get(ticket, "No coat found for that ticket")
print(claim)
```

Output:

```
No coat found for that ticket
```

Without `.get()`, this line would have been `counter[60]`, which crashes the whole program with a `KeyError` the moment a guest makes an honest mistake — exactly the situation `.get()` exists to handle gracefully.

**4. A new coat comes in, reusing a ticket number that was already claimed and returned.**

```python
counter[48] = {"item": "grey jacket", "counter_no": 3}
print(counter[48])
```

Output:

```
{'item': 'grey jacket', 'counter_no': 3}
```

This is the same overwrite behavior from §3.2 — assigning to an existing key replaces its value outright, which is correct here since ticket 48 was already returned and reissued.

**5. Print every remaining ticket, grouped by counter number.**

```python
by_counter = dict(sorted(counter.items(), key=lambda pair: pair[1]["counter_no"]))

for ticket_no, info in by_counter.items():
    print(f"Ticket {ticket_no}: {info['item']} (Counter {info['counter_no']})")
```

Output:

```
Ticket 48: grey jacket (Counter 3)
Ticket 47: black overcoat (Counter 2)
Ticket 49: blue umbrella (Counter 2)
```

*Common mistake: writing `counter[60]` to check whether a ticket exists, and letting a single mistaken guest crash the whole program. Whenever a key's existence isn't guaranteed, reach for `.get()` — or check `60 in counter` first — instead of looking it up directly.*

---

## 6. Summary

- A **dictionary** stores `key: value` pairs inside `{ }` and retrieves a value by its key, not by position — like a coat-check ticket retrieving one specific coat instead of scanning every hook.
- `dict[key] = value` **both adds and updates** — Python tells the two apart by whether the key already exists.
- `.keys()`, `.values()`, and `.items()` give three different views to loop over; `.items()` is the one to reach for when you need the key and value together.
- **Sorting** means building a sorted list of a dictionary's items — the dictionary itself has no order of its own to rearrange.
- **Nested dictionaries** represent one record made of several named fields, some of which are themselves records.
- `.get()` and `.setdefault()` prevent a missing key from crashing your program with a `KeyError`.
- A **dictionary comprehension** builds — or, by swapping key and value, inverts — a dictionary in one line; inverting is only safe when the original values are unique.

Up next: iterators, generators, and the `collections` module — the machinery quietly running behind almost every `for` loop you've written so far.

---

*© 2026 Revature · AI Native Engineering — Foundations · Unit 3.4 · Version 1.0*

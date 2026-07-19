# Sets

---

## 1. Learning Objectives

By the end of this unit, you will be able to:

✓ Create a set and explain exactly what "uniqueness" means for the values inside it.  
✓ Explain why checking membership in a set is fast, in plain terms — no math background needed.  
✓ Compare two sets using union, intersection, difference, and symmetric difference.  
✓ Add, remove, and safely discard elements from a set, and write a simple set comprehension.  
✓ Compare two sets as wholes with subset, superset, and disjoint checks, and know when a `frozenset` is the right choice.  
✓ Recognize a "silent duplicate" bug caused by treating two different-looking values as the same, and fix it.

---

## 2. Overview

The last unit closed with a promise: sets give you a guarantee neither the list nor the tuple does. A Python **set** is an unordered collection where every value can appear at most once — put a duplicate in, and it's simply dropped, automatically, with no effort from you. Because a set doesn't care about position, the question you ask it is almost always "is this in here?" rather than "what's in position 3?"

Picture a stamp collector's album. Each page has exactly one slot per design — you'd never paste two identical "Gandhi Centenary" stamps into the same album; a duplicate just gets set aside for trading. The album doesn't care what order the stamps went in, either — flipping to a page, you only ever ask "do I have this one or not?" That's exactly the guarantee a Python set gives you.

This unit covers creating sets, why membership checks on them are fast, the four ways to compare two sets, adding and removing values safely, set comprehensions, comparing sets as wholes, and the frozen, unchangeable version of a set.

---

## 3. Description

### 3.1 Creating a Set — the Uniqueness Guarantee

A set is written with comma-separated values inside curly braces `{ }`. If you write the same value twice, Python keeps only one copy — no error, no warning, it just quietly enforces the rule.

```python
collector_a = {"India Independence", "Gandhi Centenary", "Wildlife of India", "India Independence"}
print(collector_a)
```

Output:

```
{'India Independence', 'Gandhi Centenary', 'Wildlife of India'}
```

`"India Independence"` was typed twice but appears once — that's the uniqueness guarantee doing its job, not a mistake in the output.

**A trap worth knowing now, before it costs you a debugging session:** empty curly braces do **not** make an empty set.

```python
empty_set = set()
looks_like_a_set = {}

print(type(empty_set))
print(type(looks_like_a_set))
```

Output:

```
<class 'set'>
<class 'dict'>
```

`{}` was claimed by dictionaries (the next unit) long before sets existed as a separate idea, so Python reads bare `{}` as an empty dictionary. To get an empty set, you must write `set()` explicitly.

### 3.2 Why Membership Checks Are Fast

With a list, asking "is this value in here?" means Python checks the first element, then the second, then the third — one at a time — until it finds a match or runs out of elements. For a list of ten items that's instant either way; for a list of ten million, it genuinely matters.

A set avoids that scan entirely. Instead of storing values in a plain sequence, Python keeps them in an internal lookup structure — similar to how a library catalogs books by a call number instead of making you walk every shelf. Give the set a value, and it computes where that value *should* live in its internal structure and checks straight there. That's why `in` on a set stays fast even as the set grows, while `in` on a list gets slower as the list grows.

```python
collector_b = {"Gandhi Centenary", "Space Missions", "Wildlife of India"}
print("Space Missions" in collector_b)
print("Moon Landing" in collector_b)
```

Output:

```
True
False
```

### 3.3 Comparing Two Sets

This is where sets earn their keep — the moment you have two collections and need to know how they relate.

```python
collector_a = {"India Independence", "Gandhi Centenary", "Wildlife of India"}
collector_b = {"Gandhi Centenary", "Space Missions", "Wildlife of India"}
```

| **Operation** | **Syntax** | **Meaning, in stamp-album terms** |
|---|---|---|
| Union | `collector_a \| collector_b` | Every stamp either collector owns — the combined album if they merged collections. |
| Intersection | `collector_a & collector_b` | Stamps **both** already own — no point trading for these. |
| Difference | `collector_a - collector_b` | Stamps A owns that B doesn't — what A could offer in a trade. |
| Symmetric difference | `collector_a ^ collector_b` | Stamps owned by exactly **one** of them — everything either could still want. |

```python
print(collector_a | collector_b)
print(collector_a & collector_b)
print(collector_a - collector_b)
print(collector_a ^ collector_b)
```

Output:

```
{'India Independence', 'Gandhi Centenary', 'Wildlife of India', 'Space Missions'}
{'Gandhi Centenary', 'Wildlife of India'}
{'India Independence'}
{'India Independence', 'Space Missions'}
```

Notice `collector_a - collector_b` and `collector_b - collector_a` would give *different* results — difference isn't symmetric, direction matters. Symmetric difference (`^`) is the one operation that doesn't care about direction.

### 3.4 Adding, Removing, and Discarding

```python
collector_a.add("Cybersecurity Stamps")
collector_a.discard("Wildlife of India")

print(collector_a)
```

Output:

```
{'India Independence', 'Gandhi Centenary', 'Cybersecurity Stamps'}
```

`discard()` removes a value **if it's present**, and does nothing (no error) if it isn't. `remove()` does the same job but raises a `KeyError` if the value doesn't exist. Default to `discard()` unless you specifically want your program to stop and complain when something expected turns out to be missing.

### 3.5 Set Comprehensions

A set comprehension builds a set in one line, using the same `{ }` shape you write a set literal in:

```python
name_lengths = {len(name) for name in collector_a}
print(name_lengths)
```

Output:

```
{16, 15, 20}
```

Duplicates collapse automatically here too — if two names in `collector_a` happened to have the same length, that length would still only appear once in `name_lengths`.

### 3.6 Comparing Sets as Wholes — Subset, Superset, Disjoint

Section 3.3 compared sets by combining them. Sometimes what you actually want to know is simpler: does one album sit entirely inside another?

```python
required = {"read", "write"}
user_has = {"read", "write", "delete"}

print(required <= user_has)          # True — every item on the left is also on the right
print(required.issubset(user_has))   # same check, spelled as a method
```

`<=` (or `issubset()`) asks "is everything on the left also on the right?" `>=` (or `issuperset()`) asks the mirror question. `<` and `>` add one more condition — the two sets must not be exactly equal — so a set is always a subset of itself, but never a *proper* subset of itself.

One more relation has no operator at all: `isdisjoint()` asks whether two sets share **nothing**:

```python
morning = {"asha", "rohan"}
evening = {"priya", "zara"}
print(morning.isdisjoint(evening))   # True — no overlap
```

### 3.7 Updating a Set in Place

Every operation in §3.3 builds a brand-new set and leaves the originals untouched. Each one also has an in-place twin that updates the left-hand set directly instead of building a new one:

| Builds a new set | Updates in place |
|---|---|
| `a \| b` | `a \|= b` (or `a.update(b)`) |
| `a & b` | `a &= b` |
| `a - b` | `a -= b` |
| `a ^ b` | `a ^= b` |

Reach for the in-place form when you're accumulating into one running set over time — `all_seen |= new_batch` updates `all_seen` directly, instead of building a fresh set on every pass the way `all_seen = all_seen | new_batch` would.

### 3.8 `frozenset` — a Set That Can Never Change

A tuple is a list that can't be edited after creation. A **`frozenset`** is the same idea applied to sets: once built, it has no `add()`, no `discard()` — nothing that changes its contents.

```python
fs = frozenset({"gold", "silver"})
fs.add("bronze")
```

Output:

```
AttributeError: 'frozenset' object has no attribute 'add'
```

Because it can never change, a `frozenset` is allowed to live *inside* another set — a plain `set` isn't, for the same reason a list isn't (§3.1). Reach for one whenever the thing you're tracking uniqueness of is itself an unordered group, like a pair of teammates who shouldn't count twice no matter which order they're listed in.

---

## 4. Real-World Application

A spellchecker flagging your typo as "not a word" the instant you finish typing it is §3.2's fast membership check at work — the dictionary is stored as a set of valid words, so checking your typed word against it never means scanning entries one by one.

Set comparisons show up wherever two collections need relating rather than just merging: a music app merging two playlists into one with no duplicate tracks is §3.1/3.3's union with automatic dedup; LinkedIn's "12 mutual connections" is the same **intersection** operation from §3.3, just counted instead of listed; and two QA teams comparing bug reports to see which bugs only one of them caught is **symmetric difference** — exactly the entries that showed up in one list but not the other, which is precisely what deserves a second look.

---

## 5. Worked Example

**Goal:** You're building a signup form's backend, and you need to count how many *distinct* people actually registered — except the raw data has a hidden problem sets don't fix automatically.

**1. Start with the raw signups, exactly as they came in.**

```python
signups = ["priya@mail.com", "Rahul@mail.com", "priya@mail.com", "RAHUL@mail.com", "asha@mail.com"]
```

**2. Naively convert to a set and count.**

```python
unique_emails = set(signups)
print(unique_emails)
print("Unique signups:", len(unique_emails))
```

Output:

```
{'priya@mail.com', 'Rahul@mail.com', 'RAHUL@mail.com', 'asha@mail.com'}
Unique signups: 4
```

**3. Spot the bug.** There are really only **3** distinct people — Priya, Rahul, and Asha. `"Rahul@mail.com"` and `"RAHUL@mail.com"` look like the same person to you, but a set compares values *exactly*, character by character, and `"R"` is not the same character as `"r"`. The set has no idea these refer to the same email address — it isn't wrong, it's doing precisely what you asked it to do.

**4. Fix it by normalizing before deduplicating**, using a set comprehension:

```python
unique_emails = {email.lower() for email in signups}
print(unique_emails)
print("Unique signups:", len(unique_emails))
```

Output:

```
{'priya@mail.com', 'rahul@mail.com', 'asha@mail.com'}
Unique signups: 3
```

Lowercasing every email *before* it goes into the set means two spellings of the same address collapse into one, exactly the way `"India Independence"` collapsed earlier in §3.1.

*Common mistake: assuming a set will catch "obvious" duplicates on its own. A set only ever compares values exactly as they are — if two values should be treated as equal, it's your job to make them look identical (same case, same spacing, same formatting) before they go in.*

---

## 6. Summary

- A **set** is an unordered collection, written with `{ }`, where every value can appear at most once — duplicates are dropped automatically, silently.
- `set()` makes an empty set; bare `{}` makes an empty **dictionary** — a trap worth remembering.
- Membership checks (`in`) are fast on a set because Python looks values up directly instead of scanning one by one, the way it must for a list.
- **Union (`|`), intersection (`&`), difference (`-`), and symmetric difference (`^`)** compare two sets — difference is directional, the other three are not.
- **`discard()`** removes a value safely if present; **`remove()`** raises an error if it's missing.
- **Subset (`<=`), superset (`>=`), and `isdisjoint()`** compare two sets as wholes; every set operation from §3.3 also has an **in-place** twin (`|=`, `&=`, `-=`, `^=`) that updates a set directly instead of building a new one.
- A **`frozenset`** is a set that can never be edited after creation — which is exactly what lets it live inside another set, something a regular `set` can't do.
- A set only ever compares values exactly as written — normalize your data (like lowercasing text) *before* deduplicating, or look-alike values will be treated as different.

Up next: dictionaries — a structure that pairs every value with its own key, instead of just a position or a bare presence check.

---

*© 2026 Revature · AI Native Engineering — Foundations · Unit 3.3 · Version 1.0*

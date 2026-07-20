# Python Recognition Reference

A quick-reference guide organised by what you'll **see**, not what a textbook thinks you should learn. When you spot something on a test, find it here.

---

## Variables & Types

```python
x = 5              # int (integer)
y = 3.14           # float (decimal)
name = "Andy"      # str (string)
active = True      # bool (boolean) — always capitalised: True, False
items = None       # NoneType — Python's "nothing" / "no value"
```

**Type checking:**
```python
type(x)            # Returns <class 'int'>
isinstance(x, int) # Returns True — preferred way to check type
```

**Type conversion (casting):**
```python
int("5")           # String to int → 5
str(5)             # Int to string → "5"
float("3.14")      # String to float → 3.14
bool(0)            # 0, empty string, empty list, None → False. Everything else → True.
```

---

## Strings

```python
name = "Andy"
name[0]            # "A" — zero-indexed (first character is 0, not 1)
name[-1]           # "y" — negative index counts from the end
name[1:3]          # "nd" — slice: start inclusive, end exclusive
len(name)          # 4 — length
name.upper()       # "ANDY"
name.lower()       # "andy"
name.strip()       # Removes leading/trailing whitespace
name.split(",")    # Splits string into a list at the delimiter
",".join(["a","b"])# "a,b" — joins list into string with delimiter
```

**f-strings (formatted strings) — the modern way:**
```python
f"Hello, {name}"          # "Hello, Andy"
f"Total: {5 + 3}"         # "Total: 8" — expressions inside braces
f"Price: {9.99:.2f}"      # "Price: 9.99" — format specifier
```

**Older formatting (you'll see these on tests):**
```python
"Hello, %s" % name              # %-formatting (old style)
"Hello, {}".format(name)        # .format() (middle generation)
```

---

## Data Structures

### Lists — ordered, mutable (changeable)
```python
fruits = ["apple", "banana", "cherry"]
fruits[0]              # "apple" — zero-indexed
fruits[-1]             # "cherry" — last item
fruits.append("date")  # Adds to end
fruits.insert(1, "x")  # Inserts at index 1
fruits.pop()           # Removes and returns last item
fruits.remove("apple") # Removes first occurrence by value
fruits.sort()          # Sorts in place
len(fruits)            # Number of items
"apple" in fruits      # True/False — membership test
```

### Tuples — ordered, immutable (can't change after creation)
```python
coords = (10, 20)      # Parentheses, not brackets
coords[0]              # 10 — can read, can't write
x, y = coords          # Tuple unpacking — assigns 10 to x, 20 to y
```
**Key difference from lists:** You can't add, remove, or change items. Used when data shouldn't change.

### Dictionaries — key-value pairs (like a lookup table)
```python
person = {"name": "Andy", "age": 35}
person["name"]                # "Andy" — access by key
person.get("name")            # "Andy" — same but returns None if key missing (safer)
person["email"] = "a@b.com"   # Add or update a key
person.keys()                 # dict_keys(["name", "age"])
person.values()               # dict_values(["Andy", 35])
person.items()                # dict_items([("name", "Andy"), ("age", 35)])
"name" in person              # True — checks keys, not values
del person["age"]             # Removes key-value pair
```

### Sets — unordered, unique values only
```python
unique = {1, 2, 3, 3}  # {1, 2, 3} — duplicates removed
unique.add(4)           # Add an item
a = {1, 2, 3}
b = {2, 3, 4}
a & b                   # {2, 3} — intersection
a | b                   # {1, 2, 3, 4} — union
a - b                   # {1} — difference
```

---

## Control Flow

### if / elif / else
```python
if x > 10:
    print("big")
elif x > 5:          # "else if" — only checked if the if was False
    print("medium")
else:
    print("small")
```

### Ternary (one-line if):
```python
result = "yes" if x > 5 else "no"
```

### for loops
```python
for item in fruits:           # Iterate over a list
    print(item)

for i in range(5):            # 0, 1, 2, 3, 4 — range is exclusive of end
    print(i)

for i, item in enumerate(fruits):  # Get index AND value
    print(i, item)

for key, value in person.items():  # Iterate over dictionary
    print(key, value)
```

### while loops
```python
while x > 0:
    x -= 1         # -= means "subtract and assign"
    if x == 2:
        continue   # Skip rest of this iteration, go to next
    if x == 0:
        break      # Exit loop entirely
```

---

## Functions

```python
def greet(name):              # Define a function
    return f"Hello, {name}"   # return sends a value back

greet("Andy")                 # "Hello, Andy" — calling the function

# Default parameters:
def greet(name="World"):      # Default used if no argument given
    return f"Hello, {name}"

# Multiple return values (actually returns a tuple):
def get_coords():
    return 10, 20
x, y = get_coords()          # Unpacking

# *args — variable number of positional arguments:
def add(*args):               # args becomes a tuple
    return sum(args)
add(1, 2, 3)                 # 6

# **kwargs — variable number of keyword arguments:
def info(**kwargs):           # kwargs becomes a dictionary
    print(kwargs)
info(name="Andy", age=35)    # {"name": "Andy", "age": 35}
```

### Lambda (anonymous one-line functions):
```python
double = lambda x: x * 2     # Same as def double(x): return x * 2
double(5)                     # 10
```

---

## List Comprehensions

The single most Python-specific pattern you'll see on tests.

```python
# Basic: [expression for item in iterable]
squares = [x**2 for x in range(5)]        # [0, 1, 4, 9, 16]

# With filter: [expression for item in iterable if condition]
evens = [x for x in range(10) if x % 2 == 0]  # [0, 2, 4, 6, 8]

# Dictionary comprehension:
squared = {x: x**2 for x in range(5)}     # {0: 0, 1: 1, 2: 4, 3: 9, 4: 16}

# Set comprehension:
unique_lengths = {len(word) for word in ["hi", "hello", "hey"]}  # {2, 3, 5}
```

**How to read them:** Start from the `for`, then apply the `if`, then evaluate the expression on the left.

---

## Common Operators

| Operator | Meaning | Example |
|----------|---------|---------|
| `==` | Equals | `x == 5` |
| `!=` | Not equals | `x != 5` |
| `>`, `<`, `>=`, `<=` | Comparisons | `x >= 5` |
| `and`, `or`, `not` | Logical (NOT `&&`, `\|\|`, `!`) | `x > 0 and x < 10` |
| `in` | Membership | `"a" in "apple"` |
| `is` | Identity (same object) | `x is None` |
| `%` | Modulo (remainder) | `7 % 3` → `1` |
| `//` | Floor division | `7 // 3` → `2` |
| `**` | Exponent | `2 ** 3` → `8` |
| `+=`, `-=`, `*=` | Augmented assignment | `x += 1` same as `x = x + 1` |

---

## Error Handling

```python
try:
    result = 10 / 0
except ZeroDivisionError:          # Catch specific error
    print("Can't divide by zero")
except (TypeError, ValueError):    # Catch multiple types
    print("Wrong type or value")
except Exception as e:             # Catch anything, store in e
    print(f"Error: {e}")
finally:                           # Always runs, error or not
    print("Cleanup here")
```

**Common exception types you'll see:**
- `TypeError` — wrong type (e.g., adding string + int)
- `ValueError` — right type, wrong value (e.g., int("abc"))
- `KeyError` — dictionary key doesn't exist
- `IndexError` — list index out of range
- `AttributeError` — object doesn't have that method/property
- `FileNotFoundError` — self-explanatory
- `ImportError` — module not found

---

## Classes

```python
class Dog:
    species = "Canis familiaris"    # Class variable — shared by all instances

    def __init__(self, name, age):  # Constructor — runs when you create an instance
        self.name = name            # Instance variable — unique to each instance
        self.age = age

    def bark(self):                 # Method — self is always the first parameter
        return f"{self.name} says woof!"

    def __str__(self):              # Special method — what print() shows
        return f"{self.name}, age {self.age}"

my_dog = Dog("Rex", 5)             # Create an instance
my_dog.bark()                      # "Rex says woof!"
my_dog.name                        # "Rex" — access attribute
```

### Inheritance:
```python
class Puppy(Dog):                   # Puppy inherits from Dog
    def __init__(self, name, age, trick):
        super().__init__(name, age) # Call parent's __init__
        self.trick = trick
```

**Key terms:**
- `self` — refers to the current instance (like `this` in other languages)
- `__init__` — constructor (double underscores = "dunder" methods)
- `super()` — refers to the parent class

---

## Imports

```python
import os                          # Import entire module
from os import path                # Import specific thing from module
from os import path as p           # Import with alias
import json, sys                   # Multiple imports
```

**Common standard library modules:**
- `os` — file system, environment variables
- `json` — parse/create JSON
- `re` — regular expressions
- `datetime` — dates and times
- `math` — mathematical functions
- `random` — random numbers
- `sys` — system-level operations
- `collections` — specialised data structures (Counter, defaultdict)

---

## File I/O

```python
# Reading:
with open("file.txt", "r") as f:   # "with" auto-closes the file
    content = f.read()              # Read entire file as string
    # or
    lines = f.readlines()          # Read as list of lines

# Writing:
with open("file.txt", "w") as f:   # "w" = write (overwrites), "a" = append
    f.write("Hello\n")

# JSON:
import json
with open("data.json", "r") as f:
    data = json.load(f)            # Parse JSON file into Python dict/list

with open("data.json", "w") as f:
    json.dump(data, f, indent=2)   # Write Python object as JSON
```

---

## Common Gotchas (Test Favourites)

1. **Mutable default arguments:**
   ```python
   def bad(items=[]):    # DON'T — the list is shared across all calls
   def good(items=None): # DO — create new list inside function
       items = items or []
   ```

2. **`is` vs `==`:** `==` checks value equality. `is` checks if it's the *same object in memory*. Use `is` only for `None`: `if x is None`.

3. **Integer division:** `7 / 2` → `3.5` (float division). `7 // 2` → `3` (floor division).

4. **Strings are immutable:** `name[0] = "B"` will error. You must create a new string.

5. **Indentation matters:** Python uses indentation (not braces) to define code blocks. Mixing tabs and spaces will break things.

6. **`range()` is exclusive:** `range(5)` gives `0, 1, 2, 3, 4` — not 5.

7. **Shallow vs deep copy:**
   ```python
   a = [1, [2, 3]]
   b = a.copy()        # Shallow — b[1] still points to same inner list
   import copy
   c = copy.deepcopy(a) # Deep — fully independent copy
   ```

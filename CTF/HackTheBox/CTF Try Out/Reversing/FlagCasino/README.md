# 🎰 FlagCasino

> **Category:** Reverse Engineering  
> **Difficulty:** Very Easy  
> **Platform:** Hack The Box - CTF Try Out

---

## 📖 Description

> The team stumbles into a long-abandoned casino. As you enter, the lights and music whir to life, and a staff of robots begin moving around and offering games, while skeletons of prewar patrons are slumped at slot machines. A robotic dealer waves you over and promises great wealth if you can win. Can you beat the house and gather funds for the mission?

---

## 🛠️ Skills Required

- Basic Linux usage
- Basic Reverse Engineering
- Understanding of C standard library functions

---

## 🎯 Skills Learned

- Static binary analysis
- Understanding `srand()` and `rand()`
- Recovering deterministic pseudo-random values
- Brute-force scripting

---

# Enumeration

First, identify the binary.

```bash
file FlagCasino
```

Output:

```text
ELF 64-bit LSB pie executable
```

Checking printable strings doesn't reveal the flag.

```bash
strings FlagCasino
```

So we move on to reverse engineering.

---

# Static Analysis

Opening the binary in Ghidra reveals the main logic.

```c
scanf("%c", &input);

srand(input);

value = rand();

if(value == check[i])
    printf("Correct!");
else
    exit();
```

This process repeats for every character of the flag.

The important observation is that **every character seeds the PRNG independently**.

```
Input Character
        │
        ▼
     srand()
        │
        ▼
      rand()
        │
        ▼
Compare with stored integer
```

---

# Vulnerability

The seed consists of a single byte.

```
0x00 → 0xFF
```

Only **256 possible values** exist.

Instead of reversing the comparisons manually, we simply brute-force every possible character.

---

# Solving

Python pseudocode:

```python
for seed in range(256):
    srand(seed)
    if rand() == target:
        print(chr(seed))
```

Repeating this for every stored integer reconstructs the entire flag.

---



---

# Key Takeaways

- Never use `rand()` for security.
- `rand()` is deterministic.
- Small seed spaces are trivial to brute force.
- Reverse engineering often becomes much easier by understanding program logic rather than patching binaries.

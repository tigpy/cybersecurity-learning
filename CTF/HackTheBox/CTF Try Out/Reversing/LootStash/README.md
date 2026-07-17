# 📦 LootStash

> **Category:** Reverse Engineering  
> **Difficulty:** Very Easy  
> **Platform:** Hack The Box - CTF Try Out

---

## 📖 Description

> A giant stash of powerful weapons and gear has been dropped into the arena, but there's one item you have in mind. Can you filter through the stack to get to the one thing you really need?

---

## 🛠️ Skills Required

- Linux command line
- Basic binary inspection

---

## 🎯 Skills Learned

- Using `strings`
- Identifying embedded secrets
- Static binary analysis

---

# Enumeration

Determine the binary type.

```bash
file LootStash
```

Output

```text
ELF 64-bit LSB pie executable
```

---

# String Extraction

The quickest approach is extracting printable strings.

```bash
strings LootStash
```

or

```bash
strings -n 5 LootStash
```

Among the numerous weapon and loot names, one string immediately stands out.

```text
HTB{n33dl3_1n_a_l00t_stack}
```

The challenge title itself hints at the solution.

```
LootStash

↓

Needle hidden in a huge collection
```

---

# Analysis

No reverse engineering is actually required.

The binary simply contains the flag as a printable string among many decoy strings.

---

# Flag



---

# Key Takeaways

- Always inspect binaries with `strings` first.
- Many beginner reversing challenges intentionally hide secrets inside printable data.
- Simple enumeration often solves easy reverse engineering tasks without opening a decompiler.

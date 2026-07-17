# Notes

## Commands Used

```bash
file LootStash
strings LootStash
strings -n 5 LootStash
```

---

## Observations

- ELF 64-bit PIE executable
- Flag stored directly inside binary
- No obfuscation
- No dynamic checks

---

## Technique

1. Run `strings`
2. Search for `HTB`
3. Submit flag

---

## Concepts Learned

- Binary enumeration
- Printable string extraction
- Basic reverse engineering workflow

---

## Beginner RE Checklist

Whenever analyzing a binary:

- `file`
- `checksec`
- `strings`
- `ltrace`
- `strace`
- Ghidra/IDA (if needed)

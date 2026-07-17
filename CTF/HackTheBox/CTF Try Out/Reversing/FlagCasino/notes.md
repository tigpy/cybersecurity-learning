# Notes

## Commands Used

```bash
file FlagCasino
strings FlagCasino
```

---

## Observations

- 64-bit PIE ELF
- Uses `srand()`
- Calls `rand()`
- Compares output against stored values
- Seed is a single character

---

## Technique

Instead of reversing every comparison:

1. Read stored integers.
2. Brute-force all 256 seeds.
3. Match `rand()` output.
4. Recover character.

---

## Concepts Learned

- Deterministic PRNG
- Seed brute forcing
- Static analysis
- Ghidra decompilation

# Notes

## Observations

- Modbus/TCP was the only industrial protocol present.
- Communication occurred over TCP port 502.
- Holding registers (Function Code 03) contained only zero values.
- Coil reads (Function Code 01) also returned zero.
- Vendor-specific Function Code 102 did not expose credentials.
- The flag was embedded directly inside the PCAPNG file.

---

## Useful Commands

```bash
strings traffic.pcapng

strings traffic.pcapng | grep -i "HTB"
```

---

## Key Takeaway

Not every ICS challenge requires protocol exploitation.

Always perform basic forensic analysis on supplied files before spending significant time reversing protocol traffic.

PCAPNG metadata and embedded strings can contain hidden information.

---

## Tools

- Wireshark
- strings
- grep

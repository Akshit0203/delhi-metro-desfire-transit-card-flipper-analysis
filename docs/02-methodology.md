# 02 — Methodology

## Tooling

| Tool | Role |
|------|------|
| **Flipper Zero** | Primary HF (13.56 MHz) NFC reader and DESFire directory enumerator |
| **Flipper NFC app** | Reads the card, parses the DESFire structure, exports a `.nfc` device file |
| *(optional)* **libnfc** (`nfc-list`, `nfc-anticol`) | Independent cross-check of UID / ATQA / SAK on a PC + PN532 |
| **Text editor** | Inspecting the plain-text `.nfc` dump |

## Workflow

### 1. Read
The card is placed on the Flipper Zero's HF antenna. The Flipper performs ISO 14443-3A
anticollision (recovering UID, ATQA, SAK), then activates the ISO 14443-4 transmission
protocol and issues DESFire native commands to enumerate the application directory and,
for each application, its file descriptors and key settings.

### 2. Capture
The parsed result is saved as a **Flipper NFC device file** (`.nfc`, format Version 4).
This is a human-readable, line-oriented text file — see [`../data/Delhi_metro.nfc`](../data/Delhi_metro.nfc).

### 3. Decode
Each field in the dump is interpreted against the MIFARE DESFire specification:

- **PICC Version** → manufacturer, chip type, storage size, production data.
- **ATQA / SAK** → ISO 14443-3A tag type signalling.
- **Application IDs (AIDs)** → the 3-byte identifiers of each provisioned app.
- **File Type** byte → `00` backup data, `01` standard data, `02` value, `03/04` linear/cyclic record.
- **Communication Settings** → `00` plain, `01` MACed, `03` fully enciphered.
- **Access Rights** (2 bytes / 4 nibbles) → Read / Write / Read&Write / ChangeAccessRights key refs.
- **Key Version** bytes → provisioning indicator per key slot (not key material).

### 4. Document
Raw hex is translated into the structured tables and diagrams in this repository.

## What the Flipper Can and Cannot See

| Readable **without any key** | **Not** readable without the key |
|------------------------------|----------------------------------|
| UID, ATQA, SAK, PICC version | Secret key material |
| Free memory, PICC config flags | File *contents* protected by non-`free` access rights |
| Application directory (AIDs) | Encrypted value / balance data |
| File descriptors, sizes, access rights | Anything requiring authentication |
| Key counts and key **versions** | — |

Because `Free Directory List` is enabled at the PICC and application level, the *structure*
is enumerable by any reader — which is precisely the surface this analysis documents.

## Decoding Reference Tables

**File Type byte**

| Value | Meaning |
|-------|---------|
| `00` | Standard/Backup data file (backup = atomic, rollback-safe) |
| `01` | Standard data file |
| `02` | Value file (bounded credit/debit — used for balances) |
| `04` | Record file (linear or cyclic — used for logs/transactions) |

**Communication Settings byte**

| Value | Meaning |
|-------|---------|
| `00` | Plain communication |
| `01` | MAC-protected |
| `03` | Fully enciphered (AES/3DES) |

> ℹ️ The DESFire access-rights field encodes four 4-bit key references. The symmetric
> patterns seen here (`11 11`, `22 12`, `33 13`, …) are consumer-readable descriptors,
> not secrets.

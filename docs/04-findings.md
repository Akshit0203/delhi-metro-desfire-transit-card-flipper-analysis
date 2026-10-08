# 04 — Findings & Interpretation

A distilled, annotated summary of what the dump reveals about the Delhi Metro "ONE" card.

---

## F1 — Vendor-branded AID namespace

The seven applications use AIDs `444D01`–`444D07`. The first two bytes `44 4D` decode to
ASCII **"DM"** — Delhi Metro. Sequential, human-readable AIDs make the card's provisioning
scheme transparent and confirm a single-issuer, multi-application deployment.

## F2 — One primary app, several auxiliaries

`444D01` is the workhorse: **10 key slots** and **8 files** spanning value, record, backup,
and standard-data types. `444D02` is a lightweight 5-file data app. `444D03`–`444D07` are
provisioned shells (keys all at version `00`), reserved for future services or partner use.

## F3 — Balance is stored in DESFire value files

`444D01` files `01` and `02` are **value files** (type `02`) with a `Hi Limit` of
`2147483647` (`0x7FFFFFFF`, the 32-bit signed max). Value files are the canonical DESFire
object for a stored-value balance, mutated only through authenticated `Credit`/`Debit`/
`LimitedCredit` operations. The `Limited Credit Enabled: false` flag means top-ups require
full credit authorization rather than the restricted limited-credit path.

## F4 — Transaction history via a record file

File `03` (type `04`, record) reports `Max 11 / Cur 10`: a bounded ring of records, 10 of 11
slots used. This is the typical structure for an on-card journey/transaction log.

## F5 — Atomic writes via backup files

Files `08`/`09` in `444D01` and file `08` in `444D02` are **backup data files** (type `00`),
which implement commit/abort semantics. If the card is removed from the reader field mid-write
(common on a busy fare gate), the last committed state is preserved — no partial corruption.

## F6 — Fine-grained, key-referenced access control

Each file carries a 2-byte access-rights descriptor (`11 11`, `22 12`, `33 13`, `44 14`, …).
The four nibbles map to **Read**, **Write**, **Read&Write**, and **Change-Access-Rights** key
references. Distinct per-file values show the issuer enforces least-privilege at the file
level rather than relying on a single app-wide key.

## F7 — Real keys vs reserved slots

Key-version bytes expose provisioning without exposing secrets:

- `444D01`: `44 23 6E 09 8D 61 6A 00 98 11` — mostly provisioned (production keys loaded).
- `444D02`: `D7 85 85 00 00` — partially provisioned.
- `444D03`–`444D07`: all `00` — structurally defined but unprovisioned.

## F8 — Structure is enumerable by design

`Free Directory List: true` at PICC and app level is what makes this entire analysis
possible without any key. This is a deliberate DESFire configuration choice, not a flaw: it
exposes *layout*, never *contents* or *keys*. Confidential data stays protected behind
authentication.

---

## Security Posture — Summary

| Property | Observation |
|----------|-------------|
| Platform | MIFARE DESFire — AES/3DES-capable secure element |
| Authentication | Required before any value operation (not bypassed here) |
| Key hygiene | Primary app uses multiple distinct provisioned keys |
| Data integrity | Backup files provide atomic, torn-write-safe updates |
| Access model | Per-file, key-referenced read/write separation |
| Exposed surface | Directory metadata only (by configuration) |

**Bottom line:** the card follows mainstream, defensible DESFire practice for transit
ticketing. The structural metadata is readable by design; the material that would enable
cloning or balance tampering (secret keys, authenticated payloads) is **not** exposed and was
**not** targeted.

---

## Limitations

- Analysis is based on a **single card** and a single read; values may vary across card
  generations or issue batches.
- Only **directory-level** data was captured. File *contents* behind authentication were not
  read.
- Field interpretations follow the public DESFire specification; some issuer-specific
  semantics (e.g. exact meaning of each application) are inferred, not confirmed by DMRC.

## Suggested Future Work

- Compare dumps across multiple cards/issue dates to identify which fields are static vs
  per-card.
- Document the ISO 14443-4 ATS parameters in depth (bit rates, FSCI, timing).
- Cross-validate UID/ATQA/SAK with an independent PN532 + libnfc reader.

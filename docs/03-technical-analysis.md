# 03 — Technical Analysis

A layered walkthrough of the captured dump: **PICC → Application → File → Key**.

---

## 1. Card Identification (ISO 14443-3A)

| Field | Value | Interpretation |
|-------|-------|----------------|
| Device Type | `Mifare DESFire` | NXP DESFire-family secure element |
| UID | `04 18 20 72 70 77 80` | 7-byte UID; leading `04` = NXP manufacturer code |
| ATQA | `03 44` | Answer To reQuest, Type A |
| SAK | `20` | Select AcKnowledge → card supports ISO 14443-4 |

The `SAK = 20` bit signals **ISO 14443-4 compliance**, i.e. the card speaks the block
transmission protocol (T=CL) required for DESFire APDUs.

### ISO 14443-4A ATS bytes

| Field | Value |
|-------|-------|
| T0 | `75` |
| TA(1) | `77` |
| TB(1) | `81` |
| TC(1) | `02` |
| T1…Tk | `80` |

These Answer-To-Select (ATS) bytes advertise the card's supported bit rates, frame size,
and timing parameters for the 14443-4 layer.

---

## 2. PICC Level

| Field | Value | Meaning |
|-------|-------|---------|
| PICC Version | `04 01 02 01 00 18 05 …` | Vendor/chip/size/production data block |
| Free Memory | `2592` bytes | Unallocated EEPROM (of 4096 total) |
| Change Key ID | `00` | Key used to change the PICC master key |
| Config Changeable | `true` | PICC configuration can be altered (with auth) |
| Free Create/Delete | `true` | Apps can be created/deleted without master auth |
| Free Directory List | `true` | **Directory is enumerable without auth** |
| Key Changeable | `true` | PICC master key may be rotated |
| Flags | `00` | No special PICC flags set |
| Max Keys | `01` | One key at PICC level (the master key) |
| Key 0 Version | `F1` | Version byte of the PICC master key |

**Memory math:** 4096 B total − 2592 B free = **~1504 B** committed to the 7 applications
and their 13 files.

> `Free Directory List: true` is the single most important enabler for this study: it is
> why a reader can list all seven applications and their file descriptors without presenting
> any key.

---

## 3. Application Directory

Seven applications, AIDs **`444D01` → `444D07`**. Note `44 4D` = ASCII **"DM"** (Delhi Metro).

| AID | Config Changeable | Free Create/Delete | Free Dir List | Key Changeable | Change Key ID | Max Keys |
|-----|:---:|:---:|:---:|:---:|:---:|:---:|
| `444D01` | ✓ | ✗ | ✓ | ✓ | `0E` | **0A (10)** |
| `444D02` | ✓ | ✗ | ✓ | ✓ | `0E` | 05 |
| `444D03` | ✓ | ✗ | ✓ | ✓ | `0E` | 05 |
| `444D04` | ✓ | ✗ | ✓ | ✓ | `0E` | 05 |
| `444D05` | ✓ | ✗ | ✓ | ✓ | `0E` | 05 |
| `444D06` | ✓ | ✗ | ✓ | ✓ | `0E` | 05 |
| `444D07` | ✓ | ✗ | ✓ | ✓ | `0E` | 05 |

`444D01` is clearly the **primary application**: it has double the key capacity (10 vs 5)
and carries all the value/record/backup files. `444D02` holds five simple data files, and
`444D03`–`444D07` are provisioned shells (keys all at version `00`).

---

## 4. File System — Application `444D01`

File IDs present: `00 01 02 03 05 06 08 09` (8 files).

| File | Type | Type meaning | Comm. | Access Rights | Size / Limits |
|:---:|:---:|---|:---:|:---:|---|
| `00` | `01` | Standard Data | Plain | `11 11` | 32 B |
| `01` | `02` | **Value** | Plain | `22 12` | Hi `2147483647`, Lo `2147483648`, LtdCredit 1 (off) |
| `02` | `02` | **Value** | Plain | `33 13` | Hi `2147483647`, Lo `2163276824`, LtdCredit 450 (off) |
| `03` | `04` | **Record** | Plain | `44 14` | 32 B, Max 11 / Cur 10 |
| `05` | `01` | Standard Data | Plain | `55 15` | 32 B |
| `06` | `01` | Standard Data | Plain | `66 16` | 32 B |
| `08` | `00` | Backup Data | Plain | `88 18` | 32 B |
| `09` | `00` | Backup Data | Plain | `99 19` | 32 B |

**Reading the file set:**
- **Value files (`01`, `02`)** are the DESFire primitive for a monetary balance: a signed
  integer bounded by `Lo Limit`/`Hi Limit`, mutated only by `Credit`/`Debit`/`LimitedCredit`
  commands. The large `Hi Limit` (2,147,483,647 = `0x7FFFFFFF`) is the 32-bit signed ceiling.
- **Record file (`03`, type `04`)** with `Max 11 / Cur 10` is a bounded log — e.g. recent
  journeys/transactions — filling 10 of 11 slots.
- **Backup data files (`08`, `09`, type `00`)** support atomic commit/abort, protecting
  against torn writes if the card leaves the field mid-transaction.
- **Access rights** increment per file (`11 11`, `22 12`, `33 13`, …), i.e. each file is
  bound to its own key references for Read / Write / R&W / ChangeAR.

---

## 5. File System — Application `444D02`

File IDs: `00 01 02 03 08` (5 files). All share access rights `11 21`.

| File | Type | Type meaning | Comm. | Access Rights | Size |
|:---:|:---:|---|:---:|:---:|:---:|
| `00` | `01` | Standard Data | Plain | `11 21` | 32 B |
| `01` | `01` | Standard Data | Plain | `11 21` | 32 B |
| `02` | `01` | Standard Data | Plain | `11 21` | 32 B |
| `03` | `01` | Standard Data | Plain | `11 21` | 32 B |
| `08` | `00` | Backup Data | Plain | `11 21` | 32 B |

A uniform, simpler app — four standard data files plus one backup file, likely holding
profile/configuration or secondary metadata.

---

## 6. Key Configuration

**`444D01` — 10 keys, mostly provisioned:**

| Key | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 |
|-----|---|---|---|---|---|---|---|---|---|---|
| Ver | `44` | `23` | `6E` | `09` | `8D` | `61` | `6A` | `00` | `98` | `11` |

**`444D02` — 5 keys:** `D7`, `85`, `85`, `00`, `00`

**`444D03`–`444D07` — 5 keys each, all version `00`** (reserved / unprovisioned slots).

> **Key versions are not key material.** A key version is a public 1-byte label that lets a
> back-end track which generation of a key is loaded. Non-zero versions on `444D01`/`444D02`
> indicate real provisioned keys; the all-`00` pattern on the auxiliary apps indicates slots
> that exist structurally but have not been loaded with production keys.

---

## 7. Communication Security Observation

Every file descriptor reports **Communication Settings = `00` (plain)** at the directory
level. This describes how file *descriptors* are exposed, not a claim that fare transactions
run unauthenticated — DESFire requires mutual authentication before any `Credit`/`Debit` on a
value file regardless of a file's comm-setting label, and the actual value payloads were never
read in this study.

Continue to [`04-findings.md`](04-findings.md) for the interpreted conclusions.

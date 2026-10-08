# 05 — Complete Card Architecture Model

A full, field-by-field reconstruction of the card from the captured `.nfc` dump.
Source: [`../data/Delhi_metro.nfc`](../data/Delhi_metro.nfc).

---

## PICC (card root)

| Field | Value |
|-------|-------|
| Filetype | Flipper NFC device, Version 4 |
| Device type | Mifare DESFire |
| UID | `04 18 20 72 70 77 80` |
| ATQA | `03 44` |
| SAK | `20` |
| T0 / TA(1) / TB(1) / TC(1) / T1…Tk | `75` / `77` / `81` / `02` / `80` |
| PICC Version | `04 01 02 01 00 18 05 04 01 01 01 04 18 05 04 18 20 72 70 77 80 B9 0C 23 41 10 01 23` |
| Free Memory | `2592` |
| Change Key ID | `00` |
| Config Changeable | `true` |
| Free Create/Delete | `true` |
| Free Directory List | `true` |
| Key Changeable | `true` |
| Flags | `00` |
| Max Keys | `01` |
| Key 0 Version | `F1` |
| Application Count | `7` |

**Application IDs:** `44 4D 01 · 44 4D 02 · 44 4D 03 · 44 4D 04 · 44 4D 05 · 44 4D 06 · 44 4D 07`

---

## Application `444D01`

| Setting | Value |
|---------|-------|
| Change Key ID | `0E` |
| Config Changeable | `true` |
| Free Create/Delete | `false` |
| Free Directory List | `true` |
| Key Changeable | `true` |
| Flags | `00` |
| Max Keys | `0A` (10) |

**Key versions:** `44 · 23 · 6E · 09 · 8D · 61 · 6A · 00 · 98 · 11`

**Files** (`00 01 02 03 05 06 08 09`):

| File | Type | Comm | Access Rights | Details |
|:---:|:---:|:---:|:---:|---|
| `00` | `01` Standard | `00` | `11 11` | Size 32 |
| `01` | `02` Value | `00` | `22 12` | Hi `2147483647`, Lo `2147483648`, LtdCredit `1`, LtdCredit Enabled `false` |
| `02` | `02` Value | `00` | `33 13` | Hi `2147483647`, Lo `2163276824`, LtdCredit `450`, LtdCredit Enabled `false` |
| `03` | `04` Record | `00` | `44 14` | Size 32, Max `11`, Cur `10` |
| `05` | `01` Standard | `00` | `55 15` | Size 32 |
| `06` | `01` Standard | `00` | `66 16` | Size 32 |
| `08` | `00` Backup | `00` | `88 18` | Size 32 |
| `09` | `00` Backup | `00` | `99 19` | Size 32 |

---

## Application `444D02`

| Setting | Value |
|---------|-------|
| Change Key ID | `0E` |
| Config Changeable | `true` |
| Free Create/Delete | `false` |
| Free Directory List | `true` |
| Key Changeable | `true` |
| Flags | `00` |
| Max Keys | `05` |

**Key versions:** `D7 · 85 · 85 · 00 · 00`

**Files** (`00 01 02 03 08`):

| File | Type | Comm | Access Rights | Details |
|:---:|:---:|:---:|:---:|---|
| `00` | `01` Standard | `00` | `11 21` | Size 32 |
| `01` | `01` Standard | `00` | `11 21` | Size 32 |
| `02` | `01` Standard | `00` | `11 21` | Size 32 |
| `03` | `01` Standard | `00` | `11 21` | Size 32 |
| `08` | `00` Backup | `00` | `11 21` | Size 32 |

---

## Applications `444D03` – `444D07`

All five share an identical configuration and are provisioned shells (no files listed in the
dump, all key versions `00`).

| Setting | Value (all five) |
|---------|------------------|
| Change Key ID | `0E` |
| Config Changeable | `true` |
| Free Create/Delete | `false` |
| Free Directory List | `true` |
| Key Changeable | `true` |
| Flags | `00` |
| Max Keys | `05` |
| Key versions | `00 · 00 · 00 · 00 · 00` |

---

## Decoding Legend

**File Type** — `00` Backup data · `01` Standard data · `02` Value · `04` Record
**Communication Settings** — `00` Plain · `01` MACed · `03` Enciphered
**Access Rights** — two bytes → four 4-bit key references: Read · Write · Read&Write · ChangeAR
**Key Version** — public 1-byte provisioning label (not key material)
**Value limits** — `Hi Limit` = `0x7FFFFFFF` = 2,147,483,647 (32-bit signed max)

# 01 — Objective & Scope

## Objective

To analyze the data structure, security features, and configuration of a Delhi Metro
**MIFARE DESFire** smart card (the "ONE" card) using NFC technology and the **Flipper Zero**,
and to produce a clear, reproducible technical model of how the card is architected.

## Research Questions

1. **Identity** — What protocol-level identifiers (UID, ATQA, SAK) and ISO 14443-4
   attributes does the card present?
2. **Memory** — How much EEPROM does the card carry, how much is free, and what PICC-level
   configuration flags are set?
3. **Applications** — How many applications are provisioned, how are their AIDs allocated,
   and how do their capabilities differ?
4. **File system** — What file types are used (standard data, backup, value, record), how
   large are they, and what access-control rules govern them?
5. **Cryptography** — How many keys does each application declare, and what do the key
   *versions* reveal about which slots are provisioned?

## In Scope

- Passive, read-only enumeration of publicly readable DESFire directory metadata.
- Structural interpretation of the captured `.nfc` dump.
- Educational documentation of the card's architecture.

## Out of Scope (explicitly excluded)

- Recovering or brute-forcing secret cryptographic keys.
- Reading or modifying the stored fare balance.
- Cloning, emulating, or replaying the card.
- Any interaction with DMRC back-end systems or live fare gates.

## Standards & Technology Under Study

- **Chip:** NXP MIFARE DESFire
- **Protocol:** ISO/IEC 14443 Type A, Parts 3 (anticollision) & 4 (transmission protocol)
- **Frequency:** 13.56 MHz (HF)

See [`02-methodology.md`](02-methodology.md) for how the analysis was performed.

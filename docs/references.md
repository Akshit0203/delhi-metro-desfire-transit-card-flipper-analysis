# 06 — References & Further Reading

## Standards

- **ISO/IEC 14443-1:2018** — Identification cards — Contactless ICC — Part 1: Physical characteristics
- **ISO/IEC 14443-2** — Part 2: Radio frequency power and signal interface (13.56 MHz)
- **ISO/IEC 14443-3** — Part 3: Initialization and anticollision (ATQA, UID, SAK)
- **ISO/IEC 14443-4** — Part 4: Transmission protocol (T=CL, ATS, block framing)
- **ISO/IEC 7816-4** — APDU structure referenced by DESFire's ISO-wrapped command set

## Chip & Platform

- NXP Semiconductors — *MIFARE DESFire* product family (overview, feature set)
- NXP — MIFARE DESFire EV1/EV2/EV3 datasheets and application notes (file types, value files, access rights, key versioning)
- NXP AN — *MIFARE DESFire as Type 4 Tag* / NDEF mapping (background on DESFire file model)

## Tooling

- **Flipper Zero** — Official NFC documentation (reading, `.nfc` device file format v4)
- **Flipper Zero** — DESFire support notes in the NFC app
- **libnfc** — `nfc-list`, `nfc-anticol` utilities for PN532-based cross-checks
- **libfreefare** — DESFire-aware library for independent enumeration

## Background Reading

- Transit fare-collection architectures using stored-value smart cards
- DESFire access-rights and authentication model (mutual auth before Credit/Debit)
- Contactless payment/transit security — threat models and defensive design

## Primary Artifact

- [`../data/Delhi_metro.nfc`](../data/Delhi_metro.nfc) — the raw Flipper NFC device file analyzed in this repository
- [`../data/raw/Delhi_metro_nfc.pdf`](../data/raw/Delhi_metro_nfc.pdf) — original exported dump (PDF)

> Specifications are cited for educational context. Where issuer-specific semantics are
> inferred (e.g. the exact role of each application), this is noted in
> [`04-findings.md`](04-findings.md) under *Limitations*.

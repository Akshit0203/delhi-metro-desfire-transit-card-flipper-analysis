# ⚖️ Legal & Ethical Disclaimer

This repository documents a **passive, read-only** technical analysis of a MIFARE DESFire
smart card (a Delhi Metro "ONE" card) performed with a Flipper Zero, published **strictly for
educational and security-research purposes**.

## Scope of the Work

- The analysis was performed on a card that is the **author's own, legally obtained** property.
- The process was **non-invasive and read-only**. Nothing was written to, recharged,
  debited, cloned, emulated, or otherwise modified on the card.
- Only **structural metadata** exposed by the card to any compliant ISO/IEC 14443-4 reader is
  documented — card identifiers, memory configuration, application directory, file
  descriptors, access-right descriptors, and key **version** bytes.
- **No secret cryptographic key material was recovered or disclosed.** Key *versions* are
  public labels and reveal nothing about the underlying keys.
- **No stored value, balance, or fare data was read or altered.**

## What This Repository Is NOT

This project does **not** provide, and must **not** be used to attempt:

- Fare evasion, balance tampering, or unauthorized top-ups.
- Cloning, emulation, or replay of any transit card.
- Circumvention of DESFire authentication or recovery of secret keys.
- Any unauthorized access to Delhi Metro Rail Corporation (DMRC) systems or infrastructure.

## Your Responsibility

Tampering with, cloning, or defrauding a public-transport fare system is **illegal** in most
jurisdictions and may carry criminal penalties. Analyze only hardware you **own** or are
**explicitly authorized** to test. The author assumes **no liability** for any misuse of the
information in this repository.

## Trademarks

"Delhi Metro", the "ONE" card, DMRC logos, "MIFARE", "DESFire", and "Flipper Zero" are the
property of their respective owners. This project is independent and is **not affiliated with,
endorsed by, or sponsored by** DMRC, NXP Semiconductors, or Flipper Devices. All trademarks
are used nominatively for identification and educational commentary only.

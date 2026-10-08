# Contributing

Thanks for your interest in improving this NFC/DESFire analysis. Contributions that deepen the
technical accuracy or clarity of the documentation are very welcome.

## Ways to Contribute

- **Corrections** — fix a misinterpreted field, access-right nibble, or DESFire semantic.
- **Deeper analysis** — expand the decoding of PICC version bytes, ATS parameters, or file
  structures with citations to the ISO 14443 / DESFire specifications.
- **Cross-validation** — compare dumps from other cards/issue dates (with sensitive data
  redacted) to distinguish static vs per-card fields.
- **Tooling** — scripts that parse a `.nfc` dump into the tables used in `docs/`.

## Ground Rules

1. **Stay within the ethical scope.** See [`DISCLAIMER.md`](DISCLAIMER.md). This project is
   read-only, educational, and must never facilitate fraud, cloning, or key recovery.
2. **Never commit secrets.** Do not submit real cryptographic keys, private dumps, or any data
   that could identify or compromise another person's card. Redact UIDs/balances if in doubt.
3. **Only your own cards.** Any new dump you contribute must come from hardware you legally own.
4. **Cite sources.** Technical claims should reference the relevant standard or datasheet where
   possible.

## Workflow

1. Fork the repository and create a feature branch (`git checkout -b fix/record-file-notes`).
2. Make your changes with clear, descriptive commits.
3. Open a Pull Request describing *what* changed and *why*, with references.

## Style

- Markdown for all documentation; keep tables aligned and hex values upper/lower-case
  consistent with the source dump.
- Prefer small, focused PRs over large sweeping ones.

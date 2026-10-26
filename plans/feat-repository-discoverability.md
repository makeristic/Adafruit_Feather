# Feather template discoverability

## Acceptance criteria

- README describes the checked-in connector/layout template, setup, source files, and limits accurately.
- Existing attribution and upstream licence are preserved; template files are unchanged.
- GitHub description and topics identify the KiCad Feather template.
- Relative README links resolve, the diff passes whitespace checks, and the branch is pushed with an open PR.

## Checklist

- [x] Inspect source, history, existing attribution, and upstream licence (2026-10-26).
- [x] Expand README and restore the upstream licence; ignore generated editor state. (2026-10-26).
- [x] Update and verify GitHub description and topics. (2026-10-26).
- [x] Check links, review the full diff, commit, push, and open a PR (2026-10-26).

PR: https://github.com/makeristic/Adafruit_Feather/pull/1

## Correctness audit acceptance criteria

- All supplied source files are inspected and parser checks run on temporary copies.
- Demonstrably incorrect metadata or paths are corrected without speculative electrical redesign.
- A source-referenced audit report records fixed findings, unresolved risks, and validation limits.
- PR scope, validation, and branch state reflect the completed audit.

## Correctness audit checklist

- [x] Inspect source, reproduce failures, and check primary technical references (2026-10-26).
- [x] Apply and verify minimal corrections (2026-10-26).
- [x] Write the audit report and update setup/status documentation (2026-10-26).
- [ ] Review the final diff, push each commit, and update the existing PR.

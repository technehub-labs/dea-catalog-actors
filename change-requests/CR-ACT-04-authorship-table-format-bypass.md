# CR-ACT-04: Authorship Gate Table-Format Bypass (AUT-001 Coverage Hole)

| Field | Value |
|-------|-------|
| **CR** | CR-ACT-04 |
| **Title** | Authorship Gate Table-Format Bypass (AUT-001 Coverage Hole) |
| **Status** | Proposed (this PR) |
| **Layer** | L3 (Actors Catalog) |
| **Owner** | Emmanuel A. Otchere |
| **Date** | 2026-10-28 |
| **Depends on** | CR-ACT-01 (the gate being fixed) |
| **Companion to** | CR-CATALOG-STRUCT-09 (stakeholders slice, same defect class) |

## 1. Problem (proven, not inferred)

`scripts/check_authorship.py` `_extract_value` only recognizes line-format fields — a line must literally start with `**Owner**:` or `**Author**:`. Both of this repo's CRs (CR-ACT-01, CR-ACT-02) are written in the table metadata shape (`| **Author** | ... |`), so their authorship fields are entirely invisible to the "blocking" cardinal-rule gate.

Reproduction (2026-10-28, against this repo's gate): a CR carrying

```
\| **Author** \| Coder (for eaojnr) |
\| **Owner** \| Hermes |
```

passes the pre-fix `python3 scripts/check_authorship.py --strict` with **0 findings, exit 0** — both denylisted values invisible. AUT-002 cannot fire for a CR that carries no authorship field at all. The CHANGELOG's "authorship gates remain CONFORMANT" claims are true of the gate's output but overstate what is actually being checked. This is the same format-blindness class as the regenerator's Status parsing (CR-ACT-03 §1.2) — the house CR format drifted to tables while the copied validators parse one shape.

## 2. Changes (ported from CR-CATALOG-STRUCT-09, verified against this tree)

1. `_extract_value` gains an anchored table-row shape for markdown field paths (`| **Owner** | <value> |`), with one compiled pattern per field path so adjacent table keys (`| **CR** |`, `| **Companion to** |`) cannot match.
2. New AUT-002 presence rule: a CR matching the registered glob that exposes no registered authorship field at all (neither shape) fires once per file — the CR header block is present by definition, so the field is missing.
3. Five regression tests added to `tests/test_check_authorship.py` (bypass probe, allowlist-clean, adjacent-key negatives, presence rule, line-format compatibility).

## 3. Acceptance criteria

- AC-01 The bypass probe (table-format `Coder (for eaojnr)` Author + `Hermes` Owner) fires AUT-001 twice under the fixed gate (verified locally: NON-CONFORMANT).
- AC-02 `python3 scripts/check_authorship.py --strict` exits 0 on the live tree (all CR files now actually covered).
- AC-03 `python3 -m pytest tests/test_check_authorship.py -q` passes (26 tests: 21 baseline + 5 new).

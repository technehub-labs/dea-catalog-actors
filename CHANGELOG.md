# Changelog

All notable changes to the Actors Catalog (dea-catalog-actors) are documented here. Format
follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/); this
repository is governed by change requests (`change-requests/`).

## [Unreleased]

### CR status close: CR-ACT-03..06

Per the standing landing discipline (CR-ACT-03 §2.4): all four carriers close to `Merged` with landing evidence — merged as PR #7 (`a3708ba`). `CATALOG.yaml` regenerated: `open_change_requests` 4 → 0, and `repository` restored to the canonical `origin` URL form (`...dea-catalog-actors.git`) the CI regenerator step sets, which the first post-merge run flagged as drift (the local clone used the extensionless URL).

### CR-ACT-03..06: Recon 2026-10-28 defect-closure stack

Four CRs closing the defects found in the 2026-10-28 recon (same classes as the stakeholders repo's CR-CATALOG-STRUCT-08..11, landed 2026-10-27):

- **CR-ACT-03 (main-green).** `main` failed its own regenerator check. `metamodel-pointer.yaml` gains the top-level metadata block the regenerator reads; the regenerator's CR-status counter is fixed (status-token parser, both metadata shapes, CR-DEA-BC-38 closed set); landed CR statuses backfilled with PR evidence (ACT-01 → PR #5, ACT-02 → PR #6); missing index row added; `CATALOG.yaml` regenerated (`abbreviation: AC`, `metamodel_version: ^0.2.1`, `status: draft`). Consumer validation against OpenDEAM v0.2.1 re-verified exit 0.
- **CR-ACT-04 (authorship bypass).** The cardinal-rule gate was blind to table-format CR metadata (`| **Author** | ... |` — the shape of both landed CRs). `_extract_value` now extracts anchored table rows for `**...**` paths; a new AUT-002 presence rule fires when a CR exposes no authorship field at all; 5 regression tests added. The reproduced bypass (`Coder (for eaojnr)` in table format) now fires NON-CONFORMANT; the gate remains CONFORMANT on the live tree, now with all CR files actually covered.
- **CR-ACT-05 (gate honesty, wiring, id namespace, doc truth).** `validate-entity-system.yml` strict step no longer `continue-on-error` (it can actually block); the full pytest suite (58 tests) runs in `catalog-conformance.yml`; `entity-system-registry.yaml` identity flips to the org-wide form the repo's own IDM gate enforces (`actors:` prefix, hash6; `dea:` demoted to legacy) — the two id gates no longer demand mutually exclusive forms; doc truth sweep (CONTRIBUTING retitled for this repo, cardinal-rules processes glob corrected, entities README lists only gates/artifacts that exist, README aligned to the pointer and root model — Actor is L3 / `L3-work-organization` per OpenDEAM v0.2.1, not L1).
- **CR-ACT-06 (CI hygiene).** Pinned pip installs (`pyyaml==6.0.3`, `jsonschema==4.26.0`, `pytest==9.1.1`), metaframework CST checkout pinned `ref: v2.6.0`, `python-version: "3.12"` unified, `timeout-minutes` on all directly-defined jobs. Actions SHA-pinning deferred (BC-39 §8 precedent).

### CR-ACT-02: Entity System Stage 5-6 (Actors Slice)

ACT slice of the General Entity System institutionalisation (cross-repo cascade T7 of the umbrella programme). New `reconciliation/entity-system-registry.yaml` (per-repo; generic; no profiles; canonical pattern `^dea:actor-[a-z0-9-]+$`). New `scripts/check_entity_system.py` (5-rule umbrella gate; copied verbatim from `dea-catalog-processes`). New `tests/test_check_entity_system.py` (32 tests; 4 pilot-specific tests made generic for cross-repo portability; all pass). New `.github/workflows/validate-entity-system.yml` (CI). Live: gate vacuously CONFORMANT (0 entities in file_globs). D-004 clean. 118a/b/c authorship gates remain CONFORMANT.

### CR-ACT-01: Authorship Cardinal Rule (R1, R2) - Org-Wide Propagation Slice

User directive 2026-10-13: "no `Coder` / `Agent` / agent or tool names in any
authorship field of any published artifact". The CR-BP-118 cardinal rules (R1
and R2) are propagated to this repo. New blocking gate
`scripts/check_authorship.py` (CR-BP-118 / AUT-001..003), data-driven from
`reconciliation/authorship-fields.yaml` (allowlist
`{Emmanuel A. Otchere, TechNeHub Labs}`; denylist
`{Coder, Agent, Hermes, Claude, GPT, Cline, OpenAI, MiniMax, MiniMax-M3}`).
The repo's authorship-bearing field set is identical to the 118a baseline
(`**Owner**`, `**Author**` in `change-requests/CR-*.md`); no per-repo
extension is needed. CI wiring: new workflow
`.github/workflows/validate-authorship.yml` runs the gate in `--strict` mode
on every push and PR. 21/21 `test_check_authorship` pass. Carrier:
`change-requests/CR-ACT-01-authorship-cardinal-rule.md`.

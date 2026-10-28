# CR-ACT-03: Main-Green Remediation (Pointer Metadata, Counter Fix, CR Statuses, Index Row)

| Field | Value |
|-------|-------|
| **CR** | CR-ACT-03 |
| **Title** | Main-Green Remediation (Pointer Metadata, Counter Fix, CR Statuses, Index Row) |
| **Status** | Merged (PR #7) |
| **Layer** | L3 (Actors Catalog) |
| **Owner** | Emmanuel A. Otchere |
| **Date** | 2026-10-28 |
| **Depends on** | none |
| **Companion to** | CR-CATALOG-STRUCT-08 (stakeholders slice, same defect class) |

## 1. Problem (verified on `main` @ `3c1eb45`)

The `catalog-conformance` regenerator check fails on a clean checkout of `main`: `python3 scripts/regenerate_catalog.py --check` exits 3 (`CATALOG.yaml is stale`). Two independent causes, both shared with the `dea-catalog-stakeholders` repo before its CR-CATALOG-STRUCT-08 (recon 2026-10-28 reproduced the same class here):

1. **Stale `CATALOG.yaml` + pointer metadata gap.** The committed `CATALOG.yaml` carries `abbreviation: DEA-` (the regenerator's directory-name default), `metamodel_version: unknown`, and `status: active` — all three because `metamodel-pointer.yaml` carries no top-level `name`/`abbreviation`/`metamodel_version`/`conformance_status` metadata for the regenerator to read. The pointer's real `catalog.status: scaffold` sits unread one level down, and the index schema's status enum admits `draft`, not `scaffold`.
2. **Regenerator CR-status heuristic.** The vendored `count_open_change_requests` recognizes only `Status**: Merged` line shapes and a partial table heuristic; the org-consistent terminal status set (CR-DEA-BC-38) is not applied, and a doubled condition inherited from the vendored copy miscounts carriers that quote status lines.

Additionally, both landed CRs (CR-ACT-01, CR-ACT-02) still carry pre-merge statuses (`Accepted (per user directive 2026-10-13)`, `Proposed (this PR)`) with no landing evidence, and the CR index in `change-requests/README.md` is missing the CR-ACT-01 row entirely.

## 2. Changes

1. `metamodel-pointer.yaml` gains the top-level metadata block the regenerator reads (`name: Actors`, `abbreviation: AC`, `metamodel_version: "^0.2.1"`, `conformance_status: draft`, `owner`, `license`, `repository`, `homepage`), matching the pointer shape landed in the stakeholders repo. The nested allocation blocks (`metamodel:`, `catalog:`, `description:`, `links:`) are unchanged and re-verified against OpenDEAM v0.2.1 (`validate_consumer.py` exit 0).
2. `scripts/regenerate_catalog.py` updated to the fixed version: `count_open_change_requests` parses a CR status token from either house metadata shape (bold-line and table), closes on the terminal set `{Merged, Landed, Applied, Accepted, Rejected, Superseded, Withdrawn}`, and drops the duplicated condition.
3. CR statuses closed with landing evidence: CR-ACT-01 → `Merged (PR #5)`, CR-ACT-02 → `Merged (PR #6)`.
4. CR index: missing CR-ACT-01 row added; standing landing-discipline note added (every landing PR closes prior carriers' statuses and regenerates `CATALOG.yaml` in the same PR).
5. `CATALOG.yaml` regenerated: `abbreviation: AC`, `name: Actors`, `metamodel_version: ^0.2.1`, `status: draft`, truthful counts.

## 3. Acceptance criteria

- AC-01 `python3 scripts/regenerate_catalog.py --check --schema catalog-index-schema/catalog-index-schema.json` exits 0 on the new tree.
- AC-02 `python3 scripts/check_catalog_index.py --strict --schema catalog-index-schema/catalog-index-schema.json` exits 0.
- AC-03 `CATALOG.yaml` carries `abbreviation: AC`, `metamodel_version: ^0.2.1`, `status: draft`.
- AC-04 Consumer validation against OpenDEAM v0.2.1 (`dea-architecture-framework/scripts/validate_consumer.py`) still exits 0.

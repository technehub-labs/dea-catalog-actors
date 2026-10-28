# v1-alpha Catalog Entries

This directory will hold authoritative `v1-alpha` Actor catalog entries for the TechNeHub Labs Digital Enterprise Architecture (DEA) ecosystem.

Actors are stored in a **flat layout** under this directory (one subtree per record), per the org-wide entity-storage-layout spec ratified in `dea-metaframework/docs/entity-storage-layout.md`, §3. Each record will be a `<level>-<slug>-<hash>` subtree containing the record YAML. The catalog is not ECF-coordinated: Actor entries do not decompose into a 7x7 ECF matrix.

This README mirrors the structural template set in `technehub-labs/dea-catalog-processes` PR #153. The flat-layout sections below replace the containment-tree and ECF-matrix sections that are specific to ECF-coordinated catalogs.

> **Phase 2 deferred-population scaffold.** This catalog was brought up during Wave 3 of the org-wide id-system rollout; the contract is in place (schemas, id-form, conformance gates, CI) but no Phase 2 content admissions have landed yet. The CI early-exit pattern in `ci.yml` skips schema validation when the entities tree is empty; drop it when the first admission lands.

---

## 📁 Directory Topology & Naming Conventions

```
v1-alpha/
├── <level>-<slug>-<hash6>/                       # L1 record subtree
│   └── actors-<level>-<slug>-<hash6>.yaml
└── README.md
```

Subdirectory names follow `<level>-<slug>-<hash6>`. The 6-character hash is content-derived (first 4 bytes of the record file's SHA-256, base-31 over the alphabet `a-z` minus `i`, `l`, `o` plus digits `2-9`). See `dea-metaframework/docs/id-system.md` §2.5.

---

## 📄 Entity Anatomy: Actors

Per the DEA Metamodel, `actors:Actor` is the abstract kernel; concrete instances in this catalog will be specialized to `actors`.

### Canonical id forms

| Level | id form |
| :--- | :--- |
| L1: Actor | `actors:actor-<slug>-<hash6>` |

Each record file's id is content-addressed: changing the file's contents (without renaming the file) changes its hash. This is what makes the catalog content-addressed rather than directory-addressed.

### Canonical YAML Shape (illustrative; first admission will finalize)

```yaml
# id pattern: actors:actor-<slug>-<hash6>
id: actors:actor-example-slug-xxxxxx
name: Example Actor
definition: >
  The illustrative definition for an Actor record.
related_records:
  - <other-namespace>:<other-level>-...-xxxxxx
```

Cross-catalog references use the org-wide id form `<namespace>:<level>-...-<hash6>`.

---

## 📈 Population Snapshot

| Level | Count |
| :--- | ---: |
| L1 (Actor) | 0 |
| **Total** | **0** |

The catalog is empty pending Phase 2 admissions. The CI early-exit pattern in `.github/workflows/ci.yml` skips schema validation while the tree is empty.

---

## 🛡 Governance & Conformance Gates

This catalog is protected by an automated CI pipeline with the following gates. All gates run on every PR via `.github/workflows/catalog-conformance.yml` (and adjacent workflows):

1. **`validate-entries`** (CI job)
   Validates every record file against `schemas/entity.schema.json` and the cell-charter contract.
   *Why it matters:* Catches malformed records and structural drift before they reach the catalog. Currently in early-exit mode (empty tree).

2. **Catalog Conformance Suite** (CST, layout-agnostic, from `dea-metaframework/tools/conformance_test_catalog_structure.py`, checkout pinned `v2.6.0`)
   Runs the 16-test CST suite that checks id-pattern conformance, content-derived hash correctness, and the flat-layout invariants.
   *Why it matters:* Enforces the org-wide id-system contract across all 7 catalog repositories.

3. **ID System Gate** (`scripts/check_id_system.py`)
   Runs IDM-001..007 (catalog-wide) plus IDM-008 (PR-scoped coherence via `id-system-coherence-report.md` artifact).
   *Why it matters:* Catches id drift between branches and the canonical baseline before merge.

4. **Authorship Cardinal Rule gate** (`scripts/check_authorship.py`, `validate-authorship.yml`)
   Enforces AUT-001..003 over the registered authorship-bearing fields (both CR metadata shapes).
   *Why it matters:* Keeps agent/tool names out of authorship fields (CR-BP-118 as propagated by CR-ACT-01).

5. **Entity System gate** (`scripts/check_entity_system.py`, `validate-entity-system.yml`)
   Runs the 5-rule umbrella gate (ES-001..005) under `--strict`, data-driven from `reconciliation/entity-system-registry.yaml`.
   *Why it matters:* Keeps the id/relationship/lifecycle contract honest once records exist. Vacuously CONFORMANT while the tree is empty.

6. **Allocation pin** (`validate-allocation.yml`)
   Validates `metamodel-pointer.yaml` against the OpenDEAM root model (`dea-architecture-framework@v0.2.1`).
   *Why it matters:* Detects entity-mapping drift between this catalog and the root model.

7. **Catalog index + test suite** (`scripts/regenerate_catalog.py --check`, `scripts/check_catalog_index.py`, `pytest tests/`)
   Keeps `CATALOG.yaml` truthful (counts, states, paths) and runs the gate unit tests on every PR.

Not yet adopted in this repo (tracked for Phase 2 with the first admissions, do not assume they exist): a register-audit gate (`check_register_audit.py`), a CR-metadata gate (`check_cr_metadata.py`), an allocation-matrix CI job, `reconciliation/inventory.yaml`, and baseline snapshots. This catalog is not ECF-coordinated; no ECF conformance consumer applies (unlike the capabilities/processes catalogs).

---

## 🔄 Lifecycle, Reconciliation, and Baselines

This directory is the source of truth for the repository's reconciliation artifacts under `reconciliation/`:

* **`CATALOG.yaml`** (repo root): Automatically refreshed to reflect the current state of all entities in this tree. The CI gate runs `scripts/regenerate_catalog.py --check` and fails on any drift. Currently emits an empty (zero-entity) catalog.
* **`reconciliation/authorship-fields.yaml`**: The authorship-field registry the AUT gate reads (CR-level to extend).
* **`reconciliation/entity-system-registry.yaml`**: The entity-system registry the ES gate reads (canonical id form, relationships, lifecycle, per-type gates, exceptions).
* **`reconciliation/migration-id-map.yaml`**: Declared by the entity-system registry for the legacy `dea:*` sweep; created when the first admission or the T8 follow-on needs it.

When modifying this directory, contributors must run `scripts/regenerate_catalog.py` and re-run the gates (`check_id_system.py --strict`, the CST suite, `pytest tests/`) so the committed tree stays consistent. CI enforces this.

---

## 🔗 Cross-Repo References

This catalog is one of seven in the DEA ecosystem. Records reference other catalogs using the org-wide id form `<namespace>:<level>-...-<hash6>`:

| Namespace | Catalog | Status |
| --- | --- | --- |
| `actors:` | `technehub-labs/dea-catalog-actors` | This catalog |
| `orgunits:` | `technehub-labs/dea-catalog-organizational-units` | Wave 3 scaffold |
| `objects:` | `technehub-labs/dea-catalog-business-objects` | Wave 3 scaffold |
| `stakeholders:` | `technehub-labs/dea-catalog-stakeholders` | Wave 3 scaffold |
| `capabilities:` | `technehub-labs/dea-catalog-business-capabilities` | Populated (see its `CATALOG.yaml`) |
| `services:` | `technehub-labs/dea-catalog-business-services` | Populated (see its `CATALOG.yaml`) |
| `processes:` | `technehub-labs/dea-catalog-business-processes` | Populated (see its `CATALOG.yaml`) |

(Per-catalog counts live in each repo's generated `CATALOG.yaml` — hardcoded numbers here rot; the values this table previously carried were already stale.)

This catalog has secondary references to the `dea:catalog-*` self-identity records, which remain in the legacy form on purpose.

---

## 🚀 Contributor Workflow

To add the first record to this catalog:

1. **Identify the record type and metadata.** Choose the canonical name, slug, and (where applicable) the ECF domain and stage the record belongs to.
2. **File a Change Request (CR).** Create or reference an existing CR in `change-requests/` (e.g., `CR-<NS>-NN-<title>.md`).
3. **Create the YAML.** Place the file at the correct flat-layout subtree. Compute the content-derived hash for the id. Use the org-wide id form for cross-catalog references.
4. **Drop the early-exit.** Remove the empty-tree skip block from `.github/workflows/ci.yml` so schema validation runs on your new record.
5. **Run Local Conformance.** Execute `scripts/check_id_system.py --strict` and the CST suite locally. The CI pipeline runs the same gates on every PR.
6. **Regenerate Reconciliation Artifacts.** Run `scripts/regenerate_catalog.py --check --schema catalog-index-schema/catalog-index-schema.json`.
7. **Submit PR.** Link the PR to the CR. The CI pipeline enforces the gates described above.

For deeper metamodel definitions and the canonical id-system / entity-storage-layout specs, refer to:

* `technehub-labs/dea-metaframework` → `docs/id-system.md` (org-wide id contract)
* `technehub-labs/dea-metaframework` → `docs/entity-storage-layout.md` (§3 flat-layout-for-non-ECF)
* `technehub-labs/dea-metaframework` → `docs/conformance.md` (CST suite reference)
* `schemas/entity.schema.json` (record schema)

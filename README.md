# Actors

> Performers of enterprise processes — humans, teams, systems, or AI agents.

**Layer:** L3 · **Entity:** `AC` · **Metamodel:** [v0.2.1](https://github.com/technehub-labs/dea-metamodel) (OpenDEAM v0.2.1, ADR-0002)

[![View in Metamodel Explorer](https://img.shields.io/badge/Metamodel%20Explorer-View%20Entity-2C3E50?style=shield)](https://technehub-labs.github.io/metamodel/?entity=AC)

## Status

This is a **scaffold** repository — created during the Process taxonomy rollout. Content population is planned for a subsequent phase.

## Entity Definition

| Field | Value |
|-------|-------|
| Entity ID | `dea:entity-actor` |
| Class Alias | `AC` |
| Layer | L3 |
| Building Block | L3-work-organization |
| Metamodel Version | v0.2.1 |

## Catalog Structure

```
dea-catalog-actors/
├── metamodel-pointer.yaml     ← entity mapping (do not edit manually)
├── CATALOG.yaml               ← generated catalog index (do not edit manually)
├── entities/
│   └── v1-alpha/              ← catalog entries go here (Phase 2)
│       └── README.md
├── schemas/                   ← JSON Schema for this entity type
│   └── entity.schema.json
├── reconciliation/           ← gate registries (authorship, entity system)
├── scripts/                   ← conformance gates + catalog regenerator
└── .github/
    └── workflows/
        ├── ci.yml             ← validates entries against schema (early-exit while empty)
        ├── catalog-conformance.yml
        ├── validate-authorship.yml
        ├── validate-entity-system.yml
        └── validate-allocation.yml
```

## Boundary

**Actors are NOT external/affected parties.** External or affected parties are **Stakeholders** in [`dea-catalog-stakeholders`](https://github.com/technehub-labs/dea-catalog-stakeholders).

The same person can be an Actor of one process (e.g. payroll processing — they perform it) and a Stakeholder of another (e.g. performance review — they are affected by it).

## Actor types

| Value | Definition |
|---|---|
| `human` | Individual person |
| `team` | Group of humans acting as a unit |
| `system` | Software/hardware automation |
| `ai-agent` | ML/LLM/agent-based autonomous system |
| `hybrid` | Human + team + system composition |

## Contributing

See [CONTRIBUTING.md](./CONTRIBUTING.md) and the [DEA Metamodel](https://github.com/technehub-labs/dea-metamodel) for guidance.

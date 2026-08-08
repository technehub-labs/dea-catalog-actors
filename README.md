# Actors

> Performers of enterprise processes — humans, teams, systems, or AI agents.

**Layer:** L1 · **Entity:** `AC` · **Metamodel:** [v3.0.0-alpha](https://github.com/technehub-labs/dea-metamodel)

[![View in Metamodel Explorer](https://img.shields.io/badge/Metamodel%20Explorer-View%20Entity-2C3E50?style=shield)](https://technehub-labs.github.io/metamodel/?entity=Actor)

## Status

This is a **scaffold** repository — created during the Process taxonomy rollout. Content population is planned for a subsequent phase.

## Entity Definition

| Field | Value |
|-------|-------|
| Entity ID | `dea:entity-actor` |
| Class Alias | `AC` |
| Layer | L1 |
| Metamodel Version | v3.0.0-alpha |

## Catalog Structure

```
dea-catalog-actors/
├── metamodel-pointer.yaml   ← entity mapping (do not edit manually)
├── entities/
│   └── v1-alpha/          ← catalog entries go here
│       └── README.md
├── schemas/                ← JSON Schema for this entity type
│   └── entity.schema.json
└── .github/
    └── workflows/
        └── ci.yml         ← validates entries against schema
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

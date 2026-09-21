# v1-alpha Catalog Entries

This directory will hold Actor catalog entries once Phase 2 content population begins.

Each entry should be a YAML file following the schema in `../../schemas/entity.schema.json`. Example shape:

```yaml
id: actors:actor-payroll-specialist-xxxxxx
type: Actor
name: Payroll Specialist
actor_type: human
scope: departmental
description: HR operations specialist who executes internal payroll processing.
version: 1.0.0
```

See [DEA Metamodel actor schema](https://github.com/technehub-labs/dea-metamodel/blob/main/schemas/entities/actor.json) for the full canonical definition.

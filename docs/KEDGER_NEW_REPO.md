# Kedger — New Repository Plan

> **Status:** Naming locked; repo creation pending  
> **Date:** 2026-08-08  
> **Product:** Kedger  
> **Source of design truth (today):** this `google-hackathon` branch’s `docs/` until the new repo exists

---

## 1. Identity (locked)

| Field | Value |
|-------|-------|
| Name | **Kedger** |
| CLI | `kedger` |
| Schema | `kedger.memory.v1` |
| Private store | `~/.kedger/` |
| Repo policy | `<repo>/.kedger/` |
| Sealed packs | `*.kxp` |
| Not the product name | MoDeX (hackathon prototype only) |

Metaphor: *kedge* = small working anchor used to warp a ship into place → place Anchors, pull continuity forward.

---

## 2. What moves into the new repo

Copy / port these (design constitution):

- `docs/OPEN_SOURCE_MEMORY_ARCHITECTURE.md`
- `docs/MEMORY_SCHEMAS_V1.md`
- `docs/WORKSTREAM_AND_PROMOTION_V1.md`
- `docs/PARALLEL_COMPOSE_AND_HOOKS_V1.md`
- `docs/SEALED_PACKS_AND_SHAREABLE_ANCHORS_V1.md`
- `docs/IMPLEMENTATION_FROM_LITERATURE.md`
- `docs/research/` (corpus + impl recipes)
- This file

Do **not** treat as Kedger core:

- Fivetran / BigQuery / Sheet bus
- ADK multi-agent Face 2 theater
- Hackathon dashboard / judge MCP credentials
- MoDeX MCP face as the primary capture story

---

## 3. Suggested new-repo layout (Phase A)

```text
kedger/
  README.md
  LICENSE                 # Apache-2.0 preferred
  pyproject.toml          # or Rust later; v1 can be Python CLI
  docs/                   # migrated design locks
  src/kedger/
    cli/
    store/                # sqlite schema, migrations
    keys/
    ingest/
    remember/
  tests/
  hooks/                  # IDE adapter scripts calling `kedger`
```

Phase A CLI minimum:

```text
kedger keys ...
kedger ingest --from-hook
kedger remember decision|reject|constraint "..."
kedger forget <id>
kedger status
kedger doctor
```

Then Phase B sealed handoff (`handoff` / `hydrate` / `grant` / `revoke`).

---

## 4. Repo creation (blocked on human)

This agent’s GitHub token cannot create repositories under the user/org.

Please create an empty public (or private) repo named **`kedger`**, then either:

1. Point this agent at that repo / grant access, or  
2. Say “migrate now” after the empty repo exists under your account

Default owner assumption: same as the hackathon repo unless you specify otherwise.

---

## 5. Immediate next implementation step (once repo exists)

1. Bootstrap `kedger` package + `kedger` console entrypoint  
2. Private store at `~/.kedger/projects/<repo_fingerprint>/store.sqlite`  
3. Principal key bootstrap (`kedger keys`)  
4. `remember` / `forget` / `status` against Anchor schema v1  
5. Unit tests for store invariants (no silent overwrite; SUPERSEDES)

Do not deploy; do not pull Fivetran/ADK into the new core.

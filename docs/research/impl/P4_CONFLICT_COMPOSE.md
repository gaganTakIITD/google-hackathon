# P4 — Conflict & Parallel Compose (Implementation Deep-Read)

> **Pillar:** Multi-writer same workstream; Layer-1 ops + Layer-2 projection  
> **Sources:** StateFuse, TOKI, MemClaw, CRDT two-layer guides, selective supersession 2603.15994, parallel compose lock.

---

## 1. Core algorithm (implement)

```text
# Layer-1 append only
append_op(op):
  persist op immutably
  if op may conflict:
    cs = detect_conflict(op)
    if cs: persist ConflictSet(cs); do not erase losers

# Layer-2 projection
project(workstream, principal):
  ops = load_ops(workstream)
  anchors = resolve_active(ops)           # apply SUPERSEDES chains
  working = fieldwise_merge(ops.working)
  conflicts = open ConflictSets
  return View{anchors, working, conflicts}  # never writes back as silent truth
```

## 2. Conflict detection
```text
comparable if same normalized_key OR (same entities AND kind in {constraint,decision,rejection})
types: duplicate|refinement|complementary|temporal_scope|contradiction
```

## 3. MemClaw ordering fix
Run structural contradiction / supersession **before** near-duplicate rejection gate, or near-dups will 409 away legitimate SUPERSEDES.

## 4. Working merge
- Union `files_in_flight`, `open_questions`  
- If goals differ → keep primary + push alternate into `open_questions` / ConflictSet  
- Constraints never LWW by default

## 5. Tables
```sql
CREATE TABLE conflict_sets (
  id TEXT PRIMARY KEY,
  workstream_id TEXT,
  conflict_type TEXT,
  claim_ids_json TEXT,
  status TEXT,              -- open|resolved
  resolution_json TEXT,
  created_at TEXT,
  updated_at TEXT
);
```

## 6. Constants
```text
compose.constraint_policy = ESCALATE
compose.decision_policy = user_over_agent_else_ESCALATE
compose.rejection_policy = EVIDENCE_else_ESCALATE
compose.keep_audit_losers = true
```

---

## Changelog
| Date | Change |
|------|--------|
| 2026-08-08 | Initial P4 impl deep-read synthesis. |

# P3 — Anchors & Entity Graph (Implementation Deep-Read)

> **Pillar:** L3 durable judgments + entity graph + bi-temporal invalidation  
> **Sources:** Graphiti/Zep, Mem0, HippoRAG×2, A-MEM, LightRAG, GraphRAG, MIRIX Semantic/Episodic fields, RecMem, Generative Agents.

---

## 1. Mechanism distillation

### Graphiti — bi-temporal edge invalidation (copy this)
- Episode nodes non-lossy.  
- Fact edges carry `t_valid, t_invalid, t'_created, t'_expired`.  
- On contradiction with temporal overlap: set loser `t_invalid = winner.t_valid`; **keep history**.  
- Prefer newer writes transactionally.

### Mem0 — operator loop
LLM/tool chooses `ADD | UPDATE | DELETE | NOOP` after comparing to similar memories.  
**MoDeX change:** map `DELETE` → `SUPERSEDE` (soft-invalidate).

### HippoRAG / HippoRAG2 — retrieval graph, not truth
- OpenIE phrases + synonym edges; PPR from query seeds; passages as nodes in v2.  
- **MoDeX:** use for hydrate expansion; Anchors remain source of truth.

### A-MEM — atomic notes + links
- Write-time linking + neighbor evolution.  
- **MoDeX:** link-on-write yes; in-place mutation of active Anchor text no — evolve via SUPERSEDES.

### MIRIX Semantic vs Episodic fields
- Episodic: `event_type, summary, details, actor, timestamp`  
- Semantic: `name, summary, details, source`  
- **MoDeX Anchor.kind** covers decision/reject/constraint/gotcha/goal/next/open_question; map semantic-like to constraint/decision; episodic digests stay L2.

### Generative Agents — importance + reflection trees
- Score importance on write; reflect when Σ importance ≥ threshold; reflections **cite** evidence.  
- **MoDeX:** importance feeds ranking; reflection outputs are Anchor **candidates** with Evidence links.

---

## 2. SQL schema (implement)

```sql
CREATE TABLE anchors (
  id TEXT PRIMARY KEY,
  kind TEXT NOT NULL,
  statement TEXT NOT NULL,
  status TEXT NOT NULL,           -- active|superseded|disputed|forgotten
  repo_fingerprint TEXT,
  workstream_id TEXT,
  visibility TEXT,
  shareable INTEGER DEFAULT 0,
  importance REAL DEFAULT 0.5,
  confidence REAL DEFAULT 0.5,
  valid_at TEXT NOT NULL,
  invalid_at TEXT,
  created_at TEXT NOT NULL,
  updated_at TEXT NOT NULL,
  provenance_json TEXT NOT NULL,
  normalized_key TEXT
);
CREATE INDEX anc_ws_status_kind ON anchors(workstream_id, status, kind);
CREATE INDEX anc_key ON anchors(repo_fingerprint, normalized_key);

CREATE TABLE entities (
  id TEXT PRIMARY KEY,
  entity_type TEXT,
  name TEXT,
  normalized_key TEXT,
  repo_fingerprint TEXT,
  UNIQUE(repo_fingerprint, normalized_key)
);

CREATE TABLE edges (
  id TEXT PRIMARY KEY,
  src_id TEXT,
  dst_id TEXT,
  edge_type TEXT,                 -- ABOUT|DERIVES|SUPERSEDES|RELATED|NEXT_IN|EVIDENCE_FOR
  valid_at TEXT,
  invalid_at TEXT,
  created_at TEXT,
  meta_json TEXT
);
CREATE INDEX edge_src_type ON edges(src_id, edge_type);
CREATE INDEX edge_dst_type ON edges(dst_id, edge_type);

CREATE TABLE evidence (
  id TEXT PRIMARY KEY,
  supports_anchor_id TEXT,
  snippet TEXT,
  source_ref TEXT,
  weight REAL,
  visibility TEXT,
  created_at TEXT
);
```

---

## 3. SUPERSEDES algorithm (canonical)

```text
supersede(new, old):
  assert overlapping_validity(new, old) or same_normalized_key
  old.status = 'superseded'
  old.invalid_at = new.valid_at
  old.updated_at = now()
  insert new with status='active'
  insert edge SUPERSEDES(new.id, old.id)
  # optional: if old.shareable: mark shared facet stale / co-promote new
```

Conflict with constraints → create `conflicts` row; do not pick winner silently.

---

## 4. normalized_key
```text
key = hash(kind + "|" + sorted(entity_normalized_keys) + "|" + lemma(statement))
```
Use for DEDUPE / conflict grouping (TOKI keyed logging lesson).

---

## 5. Constants
| Name | Value |
|------|-------|
| `statement_max` | 280 chars |
| `recurrence_n` | 3 |
| `alias_cos_τ` | 0.80 |
| `default_importance` | 0.5 |
| `constraint_importance_floor` | 0.8 |

---

## Changelog
| Date | Change |
|------|--------|
| 2026-08-08 | Initial P3 impl deep-read synthesis. |

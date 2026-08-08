# P1 — Capture & Working Memory (Implementation Deep-Read)

> **Pillar:** Hooks → L0 Observations → L1 WorkingState  
> **Method:** Full-body reads of MemoryOS, MIRIX, MemGPT, SCM, StreamingLLM, AIOS (partial), RecMem, AgentLeak + official hook docs.  
> **Goal:** Implementation clarity for Phase A ingest + L1.

---

## 1. Coverage honesty

| Paper / doc | Depth | Role for P1 |
|-------------|-------|-------------|
| MemoryOS 2506.06326 | FULL HTML | STM pages, FIFO, chain meta |
| MIRIX 2507.07957 | FULL HTML | Core capacity, capture batching, vault |
| MemGPT 2310.08560 | FULL (prior) | Main vs FIFO vs archival |
| RecMem 2605.16045 | FULL (prior) | Cheap subconscious write path |
| SCM 2304.13343 | FULL HTML fetched | Dual buffers + controller gate |
| StreamingLLM 2309.17453 | FULL HTML | Attention sinks / retain prefixes under stream |
| Reflexion 2303.11366 | FULL HTML | Verbal critique as write candidate |
| Voyager 2305.16291 | FULL HTML | Skill library write (procedural later) |
| Claude/Cursor hooks | FULL docs | Event cadence |
| AgentLeak | FULL (prior) | Redact before multi-channel |

---

## 2. Mechanism cards (implementation-relevant)

### MemoryOS — STM page machine
- **Unit:** `page = {Q, R, T, meta_chain}`  
- **Chain meta:** LLM checks continuity with prior page; if discontinuous, **reset chain** to current; else summarize chain into `meta_chain`.  
- **Capacity:** fixed-length queue; **FIFO migrate** oldest page → MTM when full.  
- **MoDeX map:** L0 observation ≈ page; L1 goal/files ≈ chain meta summary; FIFO ≠ delete Anchors.

### MIRIX — Core + capture batching
- **Core Memory:** always-visible `persona` + `human` blocks; at **>90% capacity**, controlled rewrite (compact, don’t silently drop critical).  
- **Capture:** high-frequency raw (1.5s screenshots) → similarity skip → **batch 20** before heavy memory managers.  
- **Knowledge Vault:** credentials/secrets isolated; not mixed into semantic retrieval casually.  
- **MoDeX map:** L1 = Core; vault = never-promote `private_raw`; batching = defer cognify, not defer L0 append.

### MemGPT — OS paging
- Main context (working) + FIFO recall + archival.  
- Memory-pressure warning triggers self-directed write/summary.  
- **MoDeX map:** `PRE_COMPACT` = hard cognify; working must be reconstructed from Anchors if lost.

### RecMem — anti-eager write
- Store embeddings/raw cheaply; **LLM consolidate only under recurrence**.  
- **MoDeX map:** `llm_on_every_observation = false`; promotion counters on L0.

### SCM — controller gate
- Dual buffers; memory controller decides selective recall.  
- **MoDeX map:** hydrate is explicit capability-gated compile, not ambient full dump.

### StreamingLLM — sink tokens
- Keep initial instruction/system tokens (“attention sinks”) under streaming eviction.  
- **MoDeX map:** when injecting hydrate, pin constraints/rejects at start (and end) of context blob.

### Hooks (Claude/Cursor)
- Min set already locked: SESSION_START, USER_PROMPT, AGENT_RESPONSE, FILE_EDIT, TOOL_FAIL, PRE_COMPACT, TURN_STOP, SESSION_END.  
- SESSION_START → hydrate inject; PRE_COMPACT → hard cognify.

---

## 3. Implementation recipe (Phase A)

### Tables
```sql
CREATE TABLE observations (
  id TEXT PRIMARY KEY,
  type TEXT NOT NULL,
  ts TEXT NOT NULL,
  session_id TEXT,
  actor_principal_id TEXT,
  repo_fingerprint TEXT,
  workstream_id TEXT,
  summary TEXT,
  payload_ref TEXT,          -- blob store path
  visibility TEXT DEFAULT 'private_raw',
  redacted INTEGER DEFAULT 0
);
CREATE INDEX obs_ws_ts ON observations(workstream_id, ts);

CREATE TABLE working_state (
  workstream_id TEXT PRIMARY KEY,
  goal TEXT,
  next_step TEXT,
  files_json TEXT,           -- JSON array
  open_questions_json TEXT,
  blockers_json TEXT,
  updated_at TEXT,
  bytes INTEGER
);
```

### Ingest algorithm
```text
ingest(hook_json):
  n = normalize(hook_json)                 # adapter → ObservationType
  n.payload = redact(n.payload)
  append observations
  if n.type in {user_prompt, agent_response, file_edit, tool_fail}:
    maybe_patch_working(n)                 # heuristic extract goal/files
  bump_signal_counters(n)                  # for Tier A/B later
  if n.type in {pre_compact, session_end} or idle_boundary():
    enqueue cognify(workstream)
```

### Working patch heuristics (no LLM required v1)
- `file_edit` → add path to `files_in_flight` (cap 12, LRU)  
- User text matches `/(?:next|todo|we should)\b/i` → soft-set `next_step`  
- Explicit `modex remember` / remember-tool → L3, not only L1  
- On workstream switch → freeze old working; load/create new

### Constants
| Name | Value |
|------|-------|
| `l0_max_rows` | 5000 / workstream |
| `working_max_bytes` | 6144 |
| `files_in_flight_max` | 12 |
| `core_rewrite_ratio` | 0.90 |
| `batch_cognify_min_events` | 8 |
| `redact_before_persist` | true |

---

## 4. Anti-patterns
1. LLM extract on every keystroke/tool call (RecMem cost trap).  
2. Putting secrets into working summary (MIRIX vault separation).  
3. Letting FIFO L0 deletion remove the only copy of a judgment (promote first).  
4. Hydrate without SESSION_START path (cold start returns).

---

## Changelog
| Date | Change |
|------|--------|
| 2026-08-08 | Initial P1 impl deep-read synthesis. |

# P2 — Episode Segmentation & Cognify (Implementation Deep-Read)

> **Status:** Expanded deep-read (implementation clarity)  
> **Date:** 2026-08-08  
> **Pillar:** L0 span → boundary detect → L2 Episode → L3 promotion trigger → L4 reseal  
> **Method:** Full-body reads (arXiv HTML/PDF). Not abstract skim.  
> **Depends on:** `WORKSTREAM_AND_PROMOTION_V1.md`, hooks lock (`PRE_COMPACT`/`SESSION_END`), schemas Episode.

---

## 0. Honesty table

| Bucket | Count | Notes |
|--------|------:|-------|
| **FULL deep-read this expansion** | **14+** | ES-Mem, Membox, RecMem, Nemori, EM-LLM, MemoryOS, Graphiti episodes, MemGPT pressure, LightMem, HEMA, Cognitive Weave, MemGAS, MemoryBank (PDF), MemOS lifecycle |
| Prior FULL reused for algorithms | 8 | Already in AGENT_MEMORY / BATCH memos; re-extracted for cognify constants |
| Survey / secondary | 2 | Long-term memory surveys for bibliography pointers |
| **Abstract-only** | 0 in cards below | |

New primary FULL this pass: **ES-Mem (2601.07582)**, **Membox (2601.03785)**, **RecMem thresholds from body**, **MemoryBank (2305.10250 PDF)**.

---

## 1. Research consensus (after close reading)

Engineering/dialogue memory fails when it:

1. **Fragments first, stitches later** (Membox “fragmentation–compensation”) — turn-level vectors destroy temporal/causal continuity.  
2. Uses **fixed granularity** (every N turns) that severs decisions mid-arc (ES-Mem Fig.1 gardening→apartment pivot).  
3. Runs **eager LLM extract every turn** (RecMem cost critique).  
4. Treats **boundaries as invisible** — ES-Mem’s advance is to store boundary semantics as *retrieval anchors*, not only as cut points.  
5. Consolidates without **recurrence / heat / surprise** gates → noise Anchors.

**MoDeX stance:** Cognify on **typed boundaries**; keep L0 non-lossy until HARD flush; L2 = coherent episode with boundary descriptor; L3 promotion orthogonal and gated.

---

## 2. Mechanism cards

### 2.1 ES-Mem — EST dynamic segmentation + boundary-anchored retrieval  
**arXiv:2601.07582** · FULL

**Representation (3 levels):**
1. `bref` — refined boundary text: “Topic A ended. Transitioned to Topic B. Context: …”  
2. `si` — event summary (topics + keywords across turns)  
3. `ri` — raw context (verbatim span)

**Segmentation (2-stage):**
1. **Topic coherence:** recurrent topic extract per turn → embeddings \(H\); Pearson \(\rho_t\) across dims → mutual information \(I_t=-\frac12\log(1-\rho_t^2)\). Candidate cuts where \(I_t \le\) bottom quantile \(q\) (paper example **bottom 35%**).  
2. **Intent refinement:** local windows of \(L\) turns; LLM labels `TOPIC_SHIFT` / `TOPIC_INTRO` vs continue; boundary prob \(p_{eb}\) from high/low confidence average; keep if \(p_{eb}>\tau\).

**Retrieval:** Phase1 scan boundary anchors → Phase2 expand interval width \(w\) → Phase3 summary rerank/fuse → pull Level-3 raw.

**MoDeX lessons:**
- Store `boundary_summary` on Episode (not only `summary`).  
- Prefer dynamic events over fixed turn chunks.  
- Hydrate/`modex why` can scan boundary anchors first (coarse) then expand.

### 2.2 Membox — Topic Loom + Trace Weaver  
**arXiv:2601.03785** · FULL

**Topic Loom:** sliding window (user+agent pair); LLM classifies next message as `continuous | partial_shift | discontinuous`. Partial+full shift → **seal box**. Single-message new box unconditionally appends next message (avoid orphan turns). Sealed box \(B=\{M, topic, events, keywords\}\).

**Trace Weaver:** after seal, events vote into macro-traces via max cosine to existing events; LLM batch-verify append; unlinked → new traces. Events may belong to **multiple traces**.

**Stats (LoCoMo):** ~5–7 utterances/box; big temporal-reasoning gains vs Mem0/A-MEM; fewer context tokens.

**MoDeX lessons:**
- Soft boundary ≈ Loom seal; map to episode close.  
- `NEXT_IN` between episodes + optional `TRACE_OF` / workstream recurrence links (macro).  
- Do **not** store one Observation = one memory atom as canonical L2.

### 2.3 RecMem — recurrence-gated consolidation  
**arXiv:2605.16045** · FULL

**Layers:** Subconscious (embed+raw) → Episodic narratives → Semantic facts.  
**Trigger:** cluster neighbors with \(\cos\ge\theta_{sim}\); consolidate iff \(|\mathcal{R}_i|\ge\theta_{count}\).  
**Defaults on LoCoMo:** \(\theta_{sim}=0.7\), \(\theta_{count}=5\) (example used 2).  
**Retrieval:** \(k_{sem}=2\cdot k_{epi}\), default \(k_{epi}=10\Rightarrow k_{sem}=20\).  
**Limitation (paper):** thresholds are domain dials; recurrence ≠ salience always.

**MoDeX lessons:**
- L0 cheap always; LLM episode/Anchor distill on recurrence **or** HARD boundary.  
- Align Tier B promote with \(\theta_{count}\approx 3\)–5 (engineering denser than chat → start **3**).  
- Never auto-share from recurrence alone.

### 2.4 Nemori — EST + predict–calibrate  
**arXiv:2508.03341** · FULL (prior + re-extract)

LLM boundary detector with representation alignment; episodic narrative from conversation + `boundary_reason`; semantic distillation from **prediction error** (what model failed to predict).

**MoDeX:** soft boundary can use `boundary_reason` string; prediction-gap optional Phase C+ signal for Anchor candidates.

### 2.5 EM-LLM — Bayesian surprise + graph boundary refinement  
**arXiv:2407.09450** · FULL

Online event formation via surprise thresholding; refine boundaries with graph metrics; retrieve by **similarity + temporal contiguity**.

**MoDeX:** optional surprise detector on tool-fail bursts / large file-set jumps; always keep contiguous Evidence windows around Anchors.

### 2.6 MemoryOS — STM→MTM→LPM migration  
**arXiv:2506.06326** · FULL

- STM pages FIFO → MTM.  
- MTM segments: \(F=\cos+Jaccard>\theta\); Heat=\(\alpha N_{visit}+\beta L+\gamma e^{-\Delta t/\mu}\), \(\mu=10^7\), promote traits if Heat\(\ge\tau=5\).  
- Two-stage retrieve: top-m segments → top-k pages.

**MoDeX:** Heat feeds episode eviction ranking + Tier B signals; θ≈0.60 start.

### 2.7 Graphiti episodes  
Non-lossy episode nodes; facts derive with bi-temporal invalidation. **MoDeX:** Episode keeps `observation_ids[]`; Anchors DERIVES from Episode.

### 2.8 MemGPT pressure  
Memory-pressure warning → flush/summarize before context death. **MoDeX:** `PRE_COMPACT` = HARD cognify (non-negotiable).

### 2.9 MemoryBank — forgetting curve  
**arXiv:2305.10250** · FULL PDF  
Strength decays with Ebbinghaus-like schedule; refresh on retrieval. **MoDeX:** use decay for **Evidence/episode ranking**, not for deleting Anchors (Anchors use SUPERSEDES).

### 2.10 MemOS lifecycle  
Generate → activate → fuse → archive → expire as schedulable states. **MoDeX Episode.status:** `active|archived|expired` separate from Anchor.status.

### 2.11 LightMem / HEMA / Cognitive Weave / MemGAS  
- **LightMem:** cheap memory-augmented path — keep cognify LLM optional.  
- **HEMA:** hippocampus-inspired extended buffer — reinforces dual cheap/raw vs consolidated.  
- **Cognitive Weave:** spatio-temporal resonance graph for abstracted knowledge — optional post-episode associate.  
- **MemGAS:** multi-granularity association/selection — retrieve across episode/summary/fact grains (aligns ES-Mem levels).

---

## 3. MoDeX boundary detector (implement)

```text
BoundaryKind = HARD | SOFT | NONE

detect_boundary(obs, state) -> (BoundaryKind, reason):
  # HARD — always cognify, even if span tiny
  if obs.type in {pre_compact, session_end}:
    return HARD, obs.type
  if explicit_modex_cognify:
    return HARD, "user_cognify"

  # SOFT — cognify only if min_span met
  if workstream_switched(obs, state):
    return SOFT, "workstream_switch"
  if idle_gap(state.last_ts, obs.ts) > T_idle:
    return SOFT, "idle_gap"
  if topic_discontinuity(obs, state):          # see §3.1
    return SOFT, "topic_shift"
  if surprise_burst(obs, state):               # optional
    return SOFT, "surprise"
  return NONE, ""

# On HARD: always run cognify
# On SOFT: run iff len(span) >= min_span OR files_in_flight delta large
```

### 3.1 Topic discontinuity (v1 practical stack)

**v1 (no LLM required):**
```text
topic_discontinuity:
  if MemoryOS-style F_score(page, open_segment) < θ_segment: true
  if Jaccard(files_now, files_prev) < 0.15 and |files_now|>=2: true
  if user_prompt matches /\b(anyway|unrelated|different topic|switch to)\b/i: true
```

**v1.5 (optional Loom):**
```text
label = LLM(window=last_user+last_agent, new=obs) in {continuous, partial_shift, discontinuous}
seal if label != continuous
```

**v2 (ES-Mem-grade):**
```text
compute I_t MI sequence on topic embeddings
candidates = bottom quantile q=0.35
refine with intent LLM on ±L window; keep if p_eb > τ_intent
```

---

## 4. Cognify pipeline (canonical Phase C)

```text
cognify(workstream_id, kind, reason):
  span = observations WHERE ws AND ts > last_boundary ORDER BY ts
  if kind == SOFT and len(span) < min_span and not large_file_delta:
    log boundary_skip; return

  # --- L2 Episode ---
  boundary_summary = render_boundary(reason, span)   # ES-Mem bref style
  ep = Episode{
    id, workstream_id, repo_fingerprint,
    time_start: span[0].ts, time_end: span[-1].ts,
    summary: digest(span),                 # deterministic v1; LLM optional
    boundary_summary,
    topic_keywords: keywords(span),
    observation_ids: ids(span),
    entity_ids: entities_from(span),
    visibility: workstream_private,
    status: active,
    heat: init_heat(span),
    created_at: now
  }
  INSERT ep
  IF prev_ep: EDGE NEXT_IN(prev_ep → ep)
  # Membox Trace Weaver analogue: link macro recurrence
  link_traces(ep)   # optional: PARALLEL_WITH / CONTINUES workstream relations

  # --- L3 promotion (orthogonal) ---
  promotion.tier_A_B_C(span, ep)   # WORKSTREAM_AND_PROMOTION_V1
  # RecMem: also scan subconscious clusters with θ_sim/θ_count → candidates
  graph.associate(ep, new_anchors)  # DERIVES, ABOUT

  # --- Compose + seal L4 ---
  view = compose.project(workstream_id)   # Layer-2; may include ConflictSets
  seal.mxp(view, recipients=acl.members(ws), epoch++)

  # --- L0 hygiene ---
  mark span.compacted_at = now
  rotate payload blobs per budget          # keep ids for Evidence
  # NEVER delete Anchors; NEVER drop active constraints

  write boundary_log{kind, reason, ep_id, span_n, duration_ms}
```

### Deterministic digest (v1)
```text
Episode {time_start}–{time_end} · {workstream.slug}
Boundary: {reason}
Goal: {working.goal}
Next: {working.next_step}
Files: {top 8 files_in_flight}
Event counts: {ObservationType → n}
Explicit remembers: {remember statements}
Tool failures: {summaries}
Open questions: {working.open_questions}
```

### Boundary summary template (ES-Mem-inspired)
```text
Topic/phase "{prev_topic}" ended ({reason}).
Transitioned toward "{new_topic_or_goal}".
Hot entities: {top entities}.
```

---

## 5. SQL

```sql
CREATE TABLE episodes (
  id TEXT PRIMARY KEY,
  workstream_id TEXT NOT NULL,
  repo_fingerprint TEXT,
  time_start TEXT NOT NULL,
  time_end TEXT NOT NULL,
  summary TEXT NOT NULL,
  boundary_summary TEXT,
  topic_keywords_json TEXT,
  observation_ids_json TEXT NOT NULL,
  entity_ids_json TEXT,
  status TEXT DEFAULT 'active',          -- active|archived|expired
  heat REAL DEFAULT 0,
  n_visit INTEGER DEFAULT 0,
  visibility TEXT DEFAULT 'workstream_private',
  created_at TEXT NOT NULL
);
CREATE INDEX ep_ws_time ON episodes(workstream_id, time_start);

CREATE TABLE boundary_log (
  id TEXT PRIMARY KEY,
  workstream_id TEXT,
  kind TEXT,                             -- HARD|SOFT|SKIP
  reason TEXT,
  episode_id TEXT,
  span_n INTEGER,
  ts TEXT,
  meta_json TEXT
);

-- optional macro traces (Membox Trace Weaver)
CREATE TABLE episode_traces (
  id TEXT PRIMARY KEY,
  workstream_id TEXT,
  label TEXT,
  created_at TEXT
);
CREATE TABLE episode_trace_members (
  trace_id TEXT,
  episode_id TEXT,
  PRIMARY KEY(trace_id, episode_id)
);
```

---

## 6. Constants (start here)

| Name | Default | Source |
|------|---------|--------|
| `min_span` | 8 events | MoDeX / avoid tiny chapters |
| `T_idle` | 45 min | engineering session gap |
| `θ_segment` | 0.60 | MemoryOS F_score |
| `q_MI` | 0.35 | ES-Mem bottom quantile |
| `L_intent_window` | 2–3 turns | ES-Mem local refine |
| `τ_intent` | 0.5 | TUNE |
| `θ_sim` (RecMem) | 0.70 | RecMem LoCoMo |
| `θ_count` (RecMem/Tier B) | **3** eng / 5 chat | denser coding → 3 |
| `μ_recency` | 1e7 s | MemoryOS Heat |
| `τ_heat` | 5 | MemoryOS → Tier B signal |
| `episode_summary_max_chars` | 1600 | |
| `boundary_summary_max_chars` | 400 | |
| `hard_boundaries` | PRE_COMPACT, SESSION_END, user cognify | hooks lock |
| `llm_topic_loom` | off in v1 | Membox optional |
| `llm_episode_digest` | off in v1 | deterministic first |
| `forget_curve_on_anchors` | **false** | MemoryBank decay ≠ Anchor delete |

---

## 7. Interaction with promotion & share

| Signal | Cognify role | Share role |
|--------|--------------|------------|
| HARD boundary | Always flush episode | none |
| Recurrence \(\theta_{count}\) | Episode + Anchor **candidates** | never auto-share |
| Heat ≥ τ | Tier B promote signal | never auto-share |
| Surprise / prediction gap | Soft cognify + candidate Anchors | never auto-share |
| Explicit remember | May force mid-span Anchor without full cognify | share still explicit |

---

## 8. Eval fixtures (cognify)

1. **PreCompact flush:** force PRE_COMPACT mid-session → Episode+Anchors+`.mxp` exist even if transcript summary lossy.  
2. **Topic pivot:** auth→unrelated docs edit → SOFT boundary; two episodes; rejection in ep1 still active.  
3. **Idle gap:** 45m+ → new episode; CONTINUES relation.  
4. **Tiny HARD:** SESSION_END with 2 events → still episode+seal.  
5. **Recurrence promote:** same constraint phrasing in 3 episodes → Tier B candidate (not shareable).  
6. **No eager LLM:** 100 file_edits → 0 LLM calls if loom/digest off; only boundary cognify.  
7. **Boundary retrieve:** query “why not cookies?” hits `boundary_summary` / rejection Anchor without full L0.  
8. **Fragmentation anti-test:** turn-chunk baseline vs episode baseline on temporal handoff Q.

---

## 9. Anti-patterns

1. Fixed every-N-turns episodes (ES-Mem failure mode).  
2. One Observation = one L2 unit (Membox fragmentation).  
3. LLM cognify every tool call (RecMem).  
4. Delete Anchors on episode archive (use SUPERSEDES / status).  
5. Apply forgetting-curve deletion to constraints (MemoryBank misuse).  
6. Skip PRE_COMPACT cognify because “span small”.  
7. Auto `repo_shared_safe` from Heat/recurrence.  
8. Boundary without storing `boundary_summary` (loses ES-Mem retrieve win).

---

## 10. Recommended v1 rule set (punchline)

```text
HARD cognify: PRE_COMPACT | SESSION_END | modex cognify
SOFT cognify: workstream_switch | idle>45m | F_score<0.60 | optional Loom shift
Episode = {summary, boundary_summary, observation_ids, keywords, heat}
LLM distill: OFF by default; ON for recurrence clusters (θ_sim=0.7, θ_count=3)
After cognify: promote Tier A/B/C → compose → seal epoch++
L0 rotate after; Anchors immortal until SUPERSEDES
```

---

## Changelog

| Date | Change |
|------|--------|
| 2026-08-08 | Initial thin P2 synthesis. |
| 2026-08-08 | Major expansion: ES-Mem, Membox, RecMem thresholds, MemoryBank, boundary detector + SQL + eval fixtures. |

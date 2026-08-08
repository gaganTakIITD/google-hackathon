# P2 — Episode Segmentation & Cognify (Implementation Deep-Read)

> **Pillar:** L0 span → L2 Episode → trigger L3 promotion → L4 recompile  
> **Sources:** MemoryOS, Nemori, ES-Mem, EM-LLM, RecMem, Graphiti episodes, MemGPT pressure, hooks PRE_COMPACT.

---

## 1. Coverage honesty

| Paper | Depth | Key mechanism |
|-------|-------|---------------|
| MemoryOS 2506.06326 | FULL | Segmented paging; cos+Jaccard merge; Heat eviction |
| Nemori 2508.03341 | FULL (prior) | EST boundaries; predict–calibrate distillation |
| ES-Mem 2601.07582 | FULL (prior) | Dynamic EST; hierarchical retrieve |
| EM-LLM 2407.09450 | FULL (prior) | Bayesian surprise boundaries |
| RecMem 2605.16045 | FULL (prior) | Recurrence-gated LLM consolidate |
| Graphiti/Zep | FULL (prior) | Non-lossy episode nodes |
| LightMem 2510.18866 | FULL HTML fetched | Lightweight memory-augmented gen (details in batch memo) |

---

## 2. Boundary policy (implement exactly)

```text
HARD (always cognify):
  PRE_COMPACT, SESSION_END, explicit modex cognify

SOFT (cognify if min_span met):
  workstream_switch
  topic_discontinuity(chain_reset)          # MemoryOS
  idle_gap > T_idle
  optional: surprise/prediction_gap        # EM-LLM / Nemori

NEVER skip HARD even if span tiny — still flush working + seal.
```

---

## 3. MemoryOS segment math (port carefully)

```text
F_score(page, segment) = cos(e_s, e_p) + Jaccard(K_s, K_p)
merge if F_score > θ

Heat(segment) = α*N_visit + β*L_interaction + γ*R_recency
R_recency = exp(-Δt / μ)   # μ ≈ 1e7 seconds in paper
evict lowest Heat when MTM over capacity
if Heat > τ (=5): promote traits/facts to LPM  # MoDeX: feed promotion signals, not auto-share
```

**MoDeX adaptation:**  
- Segment ≈ Episode candidate cluster  
- Page ≈ Observation  
- LPM promotion ≈ Anchor **candidate** / Tier B signal — **not** `repo_shared_safe`

---

## 4. Cognify pipeline (Phase C)

```text
cognify(workstream_id, reason):
  span = select observations where ws AND ts > last_boundary ORDER BY ts
  if reason not in HARD and len(span) < min_span: return

  # 1) Episode
  ep = Episode(
    summary = digest(span),          # v1: deterministic template; optional LLM
    time_start=span[0].ts, time_end=span[-1].ts,
    entities = extract_paths_symbols(span),
    observation_ids = ids(span),
    visibility = workstream_private
  )
  insert ep
  edge NEXT_IN(prev_ep, ep) if prev_ep

  # 2) Anchors
  run promotion.tier_A_B_C(span, ep)  # WORKSTREAM_AND_PROMOTION_V1
  run graph.associate(ep, anchors)

  # 3) Compose + seal
  view = compose.project(workstream_id)
  seal.mxp(view, recipients=acl.members(workstream_id), epoch++)

  # 4) L0 hygiene
  mark span compacted_at; rotate payload blobs per budget
  # NEVER delete Anchors here
```

### Deterministic digest template (v1, no LLM)
```text
Episode {time_start}–{time_end} on {workstream}
Goal: {working.goal}
Files: {top files_in_flight}
Events: {counts by ObservationType}
Explicit remembers: {titles}
Tool failures: {summaries}
```

---

## 5. Constants

| Name | Default | From |
|------|---------|------|
| `θ_segment` | 0.60 | MemoryOS-style TUNE |
| `α,β,γ` heat | 1.0, 1.0, 1.0 | start equal; TUNE |
| `μ_recency` | 1e7 | MemoryOS |
| `τ_heat_promote` | 5 | MemoryOS → Tier B signal |
| `min_span` | 8 events | |
| `T_idle` | 45 min | |
| `episode_summary_max_chars` | 1600 | |

---

## 6. Anti-patterns
1. Cognify every turn (cost + noise).  
2. Drop L0 before Anchors extracted on HARD boundary.  
3. Use Heat auto-share to repo (wrong ladder).  
4. LLM summary as only truth without observation_ids provenance.

---

## Changelog
| Date | Change |
|------|--------|
| 2026-08-08 | Initial P2 impl deep-read synthesis. |

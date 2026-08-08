# P5 — Hydrate & Retrieval (Implementation Deep-Read)

> **Pillar:** L4 compile, ranking, budgets, associative expand, SESSION_START inject  
> **Sources:** Generative Agents, HippoRAG×2, MemoryOS retrieval, MIRIX Active Retrieval, Lost-in-the-Middle, MemGPT paging, GraphRAG/LightRAG.

---

## 1. Active retrieval (MIRIX — implement on SESSION_START)

```text
topic = working.goal + top_files + workstream.slug
cands = union(
  anchors by workstream ranked,
  entity_expand(topic),          # HippoRAG-like
  optional repo_shared facet
)
pack = budget_cut(cands)
inject via hook additionalContext / temp render
```

Do not rely on the model to remember to call search.

## 2. Scoring formula
```text
score = w_kind[kind]
      * (0.35*relevance + 0.25*recency + 0.20*importance_or_heat + 0.20*provenance)
      * entitled(principal)  # 0 drops
```

`w_kind`: constraint=1.0, rejection=1.0, decision=0.85, gotcha=0.7, goal=0.55, next_step=0.55, open_question=0.4

## 3. Drop order under budget
Evidence → open_questions → older episodes → gotchas → decisions → **never drop active constraints/rejections before decisions**

## 4. Lost-in-the-middle mitigation
Place top constraints/rejections at **start and end** of inject block; middle = episodes/evidence.

## 5. MemoryOS two-stage retrieve (optional for `modex why`)
1. Top-m segments/episodes by F_score  
2. Top-k pages/observations inside  
Update N_visit / recency for Heat.

## 6. Constants
| Name | Default |
|------|---------|
| handoff_kb | 32 |
| max_anchors | 40 |
| max_episodes | 3 |
| max_evidence | 8 |
| top_m_segments | 5 |
| top_k_pages | 10 |

---

## Changelog
| Date | Change |
|------|--------|
| 2026-08-08 | Initial P5 impl deep-read synthesis. |

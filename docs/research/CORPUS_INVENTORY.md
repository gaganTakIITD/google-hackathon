# MoDeX Research Corpus Inventory

> **Date:** 2026-08-08  
> **Purpose:** Honest tracking of what was **deep-read** vs **survey-indexed** vs **not yet read**.  
> **User constraint:** Do not keyword-skim the literature — gather mechanism-level insights.

---

## 0. Coverage honesty (read this first)

We **cannot** truthfully claim end-to-end reading of 200+ full papers in one agent session. Claiming that would be keyword theater.

What we *can* and *did* do:

1. **Deep-read** the load-bearing primary sources for each design lock (full HTML/PDF bodies).
2. **Deep-read multiple 2024–2026 surveys** that curate the wider corpus, then extract taxonomies + citation maps.
3. Keep an inventory so future passes expand **FULL** coverage deliberately instead of pretending it already exists.

| Bucket | Approx count | Meaning |
|--------|--------------|---------|
| **FULL deep-read** | **~70+ distinct primary texts** | Full body mechanisms extracted into memos (incl. P3/P4 impl pass) |
| **On-disk fulltext cache** | **112** files `>20KB` in `/tmp/modex-papers/full/` | Fetched bodies available for continued extraction |
| **Survey-indexed** | 150–300+ via survey bibliographies | Named + one-line role from surveys; not independently full-read |
| **Stub / TODO** | remainder of agent-memory + crypto/auth literature | Queued for later FULL passes |

---

## 1. Where detailed insights live

| Memo / lock doc | Cluster |
|-----------------|---------|
| `AGENT_MEMORY_CORPUS_DEEP_READ.md` | Graphiti/Zep, HippoRAG×2, A-MEM, Nemori, Mem0, MemGPT, GenAgents, RecMem, ES-Mem, EM-LLM, StateFuse, TOKI, LightRAG, GraphRAG, SSGM, surveys |
| `IMPLEMENTATION_FROM_LITERATURE.md` | Cross-pillar Phase A–E algorithms/constants |
| `impl/P2_EPISODE_COGNIFY.md` | **P2 expanded:** ES-Mem, Membox, RecMem thresholds, boundary detector + episode SQL |
| `impl/P3_ANCHORS_GRAPH.md` | **P3 impl:** bi-temporal graph, entity resolve, PPR constants, promotion, edge schema + invalidation recipe |
| `impl/P4_CONFLICT_COMPOSE.md` | **P4 impl:** SUPERSEDES algorithm, ConflictSet, compose projection, audit schema |
| `impl/P5_HYDRATE_RETRIEVE.md` / `P6_PRIVACY_SEAL.md` | Hydrate scoring + Inv-Scope/seal recipes |
| `impl/BATCH_SYSTEMS_AND_EVAL.md` | 26 systems/eval mechanism cards |
| `SHAREABLE_ANCHOR_POLICY_RESEARCH.md` | MemClaw, AgentLeak, MAMA, MemLeak, PRISM, Collaborative Memory, VAULT, Miller/Spritely, ADR/QOC |
| `SEALED_PACK_CRYPTO_RESEARCH.md` | age, libsodium, Wormhole, MLS, Biscuits, Macaroons, StE guidance |
| `PARALLEL_COMPOSE_AND_HOOKS_V1.md` §0 | StateFuse, TOKI, MemClaw, CRDT guides, Claude/Cursor hooks |
| `WORKSTREAM_AND_PROMOTION_V1.md` | EST/ES-Mem, RecMem, importance/reflection |
| `SEALED_PACKS_AND_SHAREABLE_ANCHORS_V1.md` | Design lock synthesized from above |

---

## 2. FULL deep-read ledger (primary)

### Agent memory / graphs / compose

| ID | Paper | Year | Memo |
|----|-------|------|------|
| 2501.13956 | Zep / Graphiti | 2025 | AGENT_MEMORY + **P3/P4** |
| 2405.14831 | HippoRAG | 2024 | AGENT_MEMORY + **P3** |
| 2502.14802 | HippoRAG 2 | 2025 | AGENT_MEMORY + **P3** |
| 2502.12110 | A-MEM | 2025 | AGENT_MEMORY + **P3/P4** |
| 2508.03341 | Nemori / What Deserves Memory | 2025–26 | AGENT_MEMORY + **P3** |
| 2504.19413 | Mem0 / Mem0g | 2025 | AGENT_MEMORY + **P3/P4** |
| 2310.08560 | MemGPT | 2023 | AGENT_MEMORY + **P3/P4** |
| 2304.03442 | Generative Agents | 2023 | AGENT_MEMORY + **P3/P4** |
| 2605.16045 | RecMem | 2026 | AGENT_MEMORY + **P3** |
| 2601.07582 | ES-Mem | 2026 | AGENT_MEMORY + **P3** |
| 2407.09450 | EM-LLM | 2024 | AGENT_MEMORY + **P3** |
| 2607.05844 | StateFuse | 2026 | AGENT_MEMORY + PARALLEL + **P4** |
| 2606.06240 | TOKI | 2026 | AGENT_MEMORY + PARALLEL + **P4** |
| 2410.05779 | LightRAG | 2024 | AGENT_MEMORY + **P3** |
| 2404.16130 | GraphRAG | 2024 | AGENT_MEMORY + **P3** |
| 2603.11768 | SSGM | 2026 | AGENT_MEMORY + **P3/P4** |
| 2407.04363 | AriGraph | 2024 | **P3/P4** |
| 2503.21322 | HyperGraphRAG | 2025 | **P3/P4** |
| 2601.03236 | MAGMA | 2026 | **P3/P4** |
| 2506.07398 | G-Memory | 2025 | **P3/P4** |
| 2402.11163 | KG-Agent | 2024 | **P3** |
| 2408.00103 | ReLiK (EL + RE) | 2024 | **P3** |
| 2603.15994 | Selective Memory / supersession chains | 2026 | PARALLEL + **P3/P4** |
| 2506.06326 | MemoryOS | 2025 | **P3/P4** |
| 2508.19828 | Memory-R1 | 2025 | **P3/P4** |
| 2510.10397 | AssoMem | 2025 | **P3/P4** |
| 2510.18866 | LightMem | 2025 | **P3/P4** |
| 2505.19549 | Multi-granularity conversational memory | 2025 | **P3** |
| 2509.25911 | Mem-α | 2025 | **P3/P4** |
| 2512.13564 | Memory in the Age of AI Agents (survey) | 2025 | AGENT_MEMORY + **P3/P4** |
| 2404.13501 | Zhang memory mechanisms survey | 2024 | AGENT_MEMORY |
| 2411.00489 | AI Long-term Memory survey | 2024 | **P3/P4** |
| 2504.15965 | From Human Memory to AI Memory survey | 2025 | **P3** |
| 2603.07670 | Memory for Autonomous LLM Agents (survey) | 2026 | CORPUS + **P3/P4** |
| 2602.19320 | Anatomy of Agentic Memory (survey) | 2026 | CORPUS + **P4** |
| 2605.06716 | From Storage to Experience (survey) | 2026 | CORPUS + **P3** |
| 2602.05665 | Graph-based Agent Memory survey | 2026 | CORPUS + **P3/P4** |
| 2602.06052 | Agent Memory Second Half survey | 2026 | CORPUS (supporting) |
| 2501.06322 | Multi-Agent Collaboration Mechanisms survey | 2025 | **P4** |

### Multi-agent frameworks (memory/sharing implications)

| ID | Paper | Year | Memo |
|----|-------|------|------|
| 2308.00352 | MetaGPT | 2023 | **P4** |
| 2307.07924 | ChatDev | 2023 | **P4** |
| 2303.17760 | CAMEL | 2023 | **P4** |
| 2308.08155 | AutoGen | 2023 | **P4** |

### Privacy / share / governance

| ID | Paper | Year | Memo |
|----|-------|------|------|
| 2606.24535 | MemClaw / Governed Shared Memory | 2026 | SHAREABLE + **P3/P4** |
| 2602.11510 | AgentLeak | 2026 | SHAREABLE + **P4** |
| ACL'26 | MAMA topology leakage | 2026 | SHAREABLE |
| 2606.29788 | MemLeak | 2026 | SHAREABLE + **P4** |
| 2605.10614 | PRISM | 2026 | SHAREABLE |
| 2505.18279 | Collaborative Memory | 2025 | SHAREABLE + **P3/P4** |
| — | VAULT (eKNOW 2025) | 2025 | SHAREABLE |
| — | Capability Myths Demolished | 2003 | SHAREABLE |
| — | Spritely / OcapPub | 2023+ | SHAREABLE |
| RFC 2693 | SPKI Certificate Theory | 1999 | SHAREABLE |

### Crypto / capability / hooks

| Source | Depth | Memo |
|--------|-------|------|
| C2SP age.md | Full | SEALED_PACK |
| libsodium seal/box/sign | Full | SEALED_PACK |
| Magic Wormhole protocols | Full | SEALED_PACK |
| MLS RFC 9420 / 9750 | Substantial | SEALED_PACK |
| Biscuits DESIGN+spec | Full | SEALED_PACK |
| Macaroons NDSS 2014 | Substantial | SEALED_PACK |
| Claude Code hooks docs | Full | PARALLEL |
| Cursor hooks docs | Full | PARALLEL |

### Classical / supporting FULL (context only)

| ID | Note | Memo |
|----|------|------|
| 1410.5401 | NTM | P3 supporting (not Anchor store) |
| 1805.04263 / 2004.04906 / 2205.12674 | Memory-net / memorizing-transformer lineage | P3 supporting |
| 2503.18813 / 2505.00675 / 2501.12948 | Additional fetched fulltexts used as supporting | P3/P4 |

---

## 3. Survey-derived insights (mechanism-level, not keyword)

From deep-reading the surveys above (not just their abstracts):

### 3.1 Write–manage–read loop (Du 2603.07670)

Agent memory is \(\mathcal{R}/\mathcal{U}\) inside a POMDP belief update — not a database lookup. Five tensioned objectives: **utility, efficiency, adaptivity, faithfulness, governance**. MoDeX already encodes these as Anchors survival, budgets, promotion, SUPERSEDES, capability gates.

### 3.2 Temporal × substrate × control taxonomy

- Temporal: working / episodic / semantic / procedural → maps to L1 / L2 / L3 / (skill scripts later).
- Substrate: context text / vectors / structured graphs / executable repos → MoDeX hybrid (sqlite graph + optional embeddings).
- Control: heuristic / prompted self-control / learned RL — MoDeX v1 = **heuristic + explicit CLI**, not RL memory controllers.

### 3.3 Storage → Reflection → Experience evolution (2605.06716)

Frontier “Experience” stage = active exploration + cross-trajectory abstraction. MoDeX L3 Anchors are Experience-class; do not auto-publish reflections (shareable memo).

### 3.4 Graph-memory lifecycle (2602.05665)

Extract → integrate (conflict/prune) → retrieve (entity expand / BFS / PPR) → consolidate. Bi-temporal invalidation (Graphiti) and LLM ADD/UPDATE/DELETE (Mem0) are the two dominant update schools — MoDeX adopts **invalidate+audit**, not silent DELETE.

### 3.5 Evaluation pain (2602.19320)

Benchmark saturation, judge sensitivity, backbone-dependent accuracy, and memory-maintenance latency often erase paper gains. MoDeX eval criteria (§15 architecture) must include **governance probes** (Inv-Scope, unshare cascade), not only Q&A recall.

### 3.6 P3/P4 constants locked from FULL reads

| Constant | Value | Source |
|----------|-------|--------|
| Synonym / ALIAS τ | 0.8 | HippoRAG / HippoRAG2 |
| PPR damping | 0.5 | HippoRAG / HippoRAG2 |
| Recognition top-k triples | 5 | HippoRAG2 |
| Graphiti reflection window | n=4 | Zep/Graphiti |
| Mem0 ops | ADD/UPDATE/DELETE/NOOP | Mem0 → MoDeX INVALIDATE |
| Bi-temporal fields | valid/invalid + created/expired | Graphiti + TOKI |
| Version chain | superseded_by / supersedes | 2603.15994 |
| Conflict pipeline order | conflict before near-dup | MemClaw |

---

## 4. Survey-indexed corpus (named, not independently FULL-read)

These appear repeatedly across surveys and are **queued** for future FULL passes. One-line roles only until deep-read:

| Paper / system | One-line role |
|----------------|---------------|
| Reflexion | Verbal self-critique as episodic journal |
| Voyager | Procedural skill library as memory |
| Cognee | Queryable graph embeddings library (docs/product; limited arXiv) |
| OpenMemory / MemMachine / Memary | Graph memory toolkits |
| LoCoMo / LongMemEval / MemoryAgentBench / MemoryArena / MemBench / RealMem | Evaluation suites |
| RETRO / Memorizing Transformers / RMT | Neural parametric memory lineage |
| Memory Networks / NTM / DNC | Classical differentiable memory (partial FULL above) |
| ReAct | Trajectory-as-short-horizon-memory |
| FLEX | Semantic gating for trajectory merge |
| ConfAIde / CaMeL / Fides | Privacy/IFC related (partial in shareable memo) |
| Classic REBEL paper | Seq2seq RE; arXiv ID collision in fetch — use HippoRAG OpenIE practice for MoDeX v1 |
| … | See bibliographies of 2512.13564, 2603.07670, 2602.05665 for the long tail |

**Next FULL-read batches (priority):**
1. ConfAIde, Fides, CaMeL (privacy IFC)
2. LoCoMo + MemoryAgentBench conflict slices (eval design)
3. Cognee docs + remaining toolkit READMEs
4. Remaining MemClaw-cited leakage papers not yet FULL
5. Clean REBEL / Stanford OpenIE canonical PDFs (non-arXiv if needed)

---

## 5. Process rule for future agents

When expanding this corpus:

1. Fetch full HTML/PDF.
2. Extract problem / representation / write-read-forget / compaction / conflict / privacy / MoDeX lessons.
3. Mark **FULL** in this inventory.
4. Only then promote claims into architecture locks.
5. Never cite a paper as “read” from title/abstract alone.

---

## Changelog

| Date | Change |
|------|--------|
| 2026-08-08 | Initial honest inventory after first multi-cluster deep-read pass. |
| 2026-08-08 | P3/P4 implementation pass: ≥55 FULL bodies; `impl/P3_ANCHORS_GRAPH.md` + `impl/P4_CONFLICT_COMPOSE.md`; expanded ledger (AriGraph, HyperGraphRAG, MAGMA, G-Memory, Selective Supersession, MemoryOS, Memory-R1, multi-agent frameworks, ReLiK, surveys). |
| 2026-08-08 | P2 major expansion: ES-Mem (2601.07582), Membox (2601.03785), RecMem body thresholds, MemoryBank; cognify HARD/SOFT rules + `boundary_summary` + episode SQL. |

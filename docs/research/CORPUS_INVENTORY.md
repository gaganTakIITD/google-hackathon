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
| **FULL deep-read** | ~56–66 distinct primary texts | Full body mechanisms extracted into memos (Batch 1 + Batch 2) |
| **Survey-indexed** | 150–300+ via survey bibliographies | Named + one-line role from surveys; not independently full-read |
| **Stub / TODO** | remainder of agent-memory + crypto/auth literature | Queued for later FULL passes |

---

## 1. Where detailed insights live

| Memo / lock doc | Cluster |
|-----------------|---------|
| `AGENT_MEMORY_CORPUS_DEEP_READ.md` | Graphiti/Zep, HippoRAG×2, A-MEM, Nemori, Mem0, MemGPT, GenAgents, RecMem, ES-Mem, EM-LLM, StateFuse, TOKI, LightRAG, GraphRAG, SSGM, surveys |
| `AGENT_MEMORY_CORPUS_DEEP_READ_BATCH2.md` | MemoryOS, LightMem, MAGMA, Memory-R1, ConfAIde, Fides, LoCoMo, MemoryAgentBench, AriGraph, Reflexion, Voyager skills, Collaborative Memory extras |
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
| 2501.13956 | Zep / Graphiti | 2025 | AGENT_MEMORY |
| 2405.14831 | HippoRAG | 2024 | AGENT_MEMORY |
| 2502.14802 | HippoRAG 2 | 2025 | AGENT_MEMORY |
| 2502.12110 | A-MEM | 2025 | AGENT_MEMORY |
| 2508.03341 | Nemori / What Deserves Memory | 2025–26 | AGENT_MEMORY |
| 2504.19413 | Mem0 | 2025 | AGENT_MEMORY |
| 2310.08560 | MemGPT | 2023 | AGENT_MEMORY |
| 2304.03442 | Generative Agents | 2023 | AGENT_MEMORY |
| 2605.16045 | RecMem | 2026 | AGENT_MEMORY |
| 2601.07582 | ES-Mem | 2026 | AGENT_MEMORY |
| 2407.09450 | EM-LLM | 2024 | AGENT_MEMORY |
| 2607.05844 | StateFuse | 2026 | AGENT_MEMORY + PARALLEL |
| 2606.06240 | TOKI | 2026 | AGENT_MEMORY + PARALLEL |
| 2410.05779 | LightRAG | 2024 | AGENT_MEMORY |
| 2404.16130 | GraphRAG | 2024 | AGENT_MEMORY |
| 2603.11768 | SSGM | 2026 | AGENT_MEMORY |
| 2512.13564 | Memory in the Age of AI Agents (survey) | 2025 | AGENT_MEMORY |
| 2404.13501 | Zhang memory mechanisms survey | 2024 | AGENT_MEMORY |
| 2603.07670 | Memory for Autonomous LLM Agents (survey) | 2026 | CORPUS (this file + extract) |
| 2602.19320 | Anatomy of Agentic Memory (survey) | 2026 | CORPUS extract |
| 2605.06716 | From Storage to Experience (survey) | 2026 | CORPUS extract |
| 2602.05665 | Graph-based Agent Memory survey | 2026 | CORPUS extract |
| 2506.06326 | MemoryOS | 2025 | AGENT_MEMORY_BATCH2 |
| 2510.18866 | LightMem | 2025 | AGENT_MEMORY_BATCH2 |
| 2601.03236 | MAGMA | 2026 | AGENT_MEMORY_BATCH2 |
| 2508.19828 | Memory-R1 | 2025 | AGENT_MEMORY_BATCH2 |
| 2407.04363 | AriGraph | 2024 | AGENT_MEMORY_BATCH2 |
| 2303.11366 | Reflexion | 2023 | AGENT_MEMORY_BATCH2 |
| 2305.16291 | Voyager (skill library) | 2023 | AGENT_MEMORY_BATCH2 |
| 2402.17753 | LoCoMo benchmark | 2024 | AGENT_MEMORY_BATCH2 |
| 2507.05257 | MemoryAgentBench | 2025 | AGENT_MEMORY_BATCH2 |

### Privacy / share / governance

| ID | Paper | Year | Memo |
|----|-------|------|------|
| 2606.24535 | MemClaw / Governed Shared Memory | 2026 | SHAREABLE |
| 2602.11510 | AgentLeak | 2026 | SHAREABLE |
| ACL'26 | MAMA topology leakage | 2026 | SHAREABLE |
| 2606.29788 | MemLeak | 2026 | SHAREABLE |
| 2605.10614 | PRISM | 2026 | SHAREABLE |
| 2505.18279 | Collaborative Memory | 2025 | SHAREABLE + BATCH2 extras |
| 2310.17884 | ConfAIde (contextual integrity) | 2023 | AGENT_MEMORY_BATCH2 |
| 2505.23643 | Fides IFC | 2025 | AGENT_MEMORY_BATCH2 (was abstract-only in SHAREABLE) |
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

---

## 4. Survey-indexed corpus (named, not independently FULL-read)

These appear repeatedly across surveys and are **queued** for future FULL passes. One-line roles only until deep-read.
*(Struck-through / removed from this stub list when promoted to §2 FULL ledger in Batch 2.)*

| Paper / system | One-line role | Status |
|----------------|---------------|--------|
| Reflexion | Verbal self-critique as episodic journal | **FULL → BATCH2** |
| Voyager | Procedural skill library as memory | **FULL → BATCH2** |
| MemoryOS | OS-inspired hierarchical paging | **FULL → BATCH2** |
| MAGMA | Multi-graph agent memory architecture | **FULL → BATCH2** |
| LightMem | Lightweight memory-augmented generation | **FULL → BATCH2** |
| Memory-R1 | RL-trained memory manage/utilize | **FULL → BATCH2** |
| AriGraph | World-model KG from observations | **FULL → BATCH2** |
| LoCoMo | Very long-term conversational memory eval | **FULL → BATCH2** |
| MemoryAgentBench | AR/TTL/LRU/SF memory-agent benchmark | **FULL → BATCH2** |
| ConfAIde | Contextual integrity privacy benchmark | **FULL → BATCH2** |
| Fides | IFC planner for AI agents | **FULL → BATCH2** |
| MemAgent | Multi-conv RL memory agent | stub |
| Omni memory | Personalized long-horizon self-evolving | stub |
| Agent KB | Cross-domain experience store | stub |
| AssoMem | Multi-signal associative retrieval | stub |
| Memento | Agent adaptation without LLM finetune | stub |
| HyperGraphRAG | Hypergraph knowledge representation | stub |
| Cognee | Queryable graph embeddings library | stub |
| OpenMemory / MemMachine / Memary | Graph memory toolkits | stub |
| LongMemEval / MemoryArena / MemBench / RealMem | Evaluation suites (LoCoMo/MAB done) | stub |
| RETRO / Memorizing Transformers / RMT | Neural parametric memory lineage | stub |
| Memory Networks / NTM / DNC | Classical differentiable memory | stub |
| ReAct | Trajectory-as-short-horizon-memory | stub |
| FLEX | Semantic gating for trajectory merge | stub |
| CaMeL (2503.18813) | Prompt-injection defense by design (Dual-LLM lineage) | stub — next privacy pass |
| … | See bibliographies of 2512.13564, 2603.07670, 2602.05665 for the long tail | |

**Next FULL-read batches (priority for engineering-memory product):**
1. CaMeL (2503.18813) + remaining MemClaw-cited leakage papers not yet FULL
2. HyperGraphRAG + RAPTOR + MemoryBank forgetting-curve details
3. LongMemEval primary paper + MemoryArena / MemBench / RealMem
4. MemAgent / MEM1 / Memento (RL / self-evolving controllers)
5. High-citation write/read/forget systems still stubbed in 2512.13564 bibliography

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
| 2026-08-08 | Batch 2: +11 FULL deep-reads (MemoryOS, LightMem, MAGMA, Memory-R1, ConfAIde, Fides, LoCoMo, MemoryAgentBench, AriGraph, Reflexion, Voyager) + Collaborative Memory extras; memo `AGENT_MEMORY_CORPUS_DEEP_READ_BATCH2.md`. Fides upgraded from abstract-only. |

# P1 — Capture & Working Memory (Implementation Recipe)

> **Pillar:** Hooks → L0 Observation → L1 WorkingState  
> **Date:** 2026-08-08  
> **Primary FULL sources:** MemoryOS (2506.06326), MemOS (2507.03724), AIOS (2403.16971), MemGPT (2310.08560), StreamingLLM (2309.17453), SCM (2304.13343), RET-LLM (2305.14322), plus LightMem / MIRIX capture cadence.  
> **Companion cards:** `BATCH_SYSTEMS_AND_EVAL.md`

---

## 0. Honesty

| Status | Papers used for P1 |
|--------|-------------------|
| FULL HTML | MemoryOS, MemOS, AIOS, MemGPT, StreamingLLM, SCM, RET-LLM, LightMem, MIRIX |
| Role | Mechanism extraction for capture buffers, working-state patches, redaction, rotation — not eval-only papers |

---

## 1. What P1 must answer

| Question | Literature answer (concrete) |
|----------|------------------------------|
| What events to observe? | Dialogue pages `{Q,R,T}` (MemoryOS); agent syscalls / tool results (AIOS); screen/IDE frames with debounce (MIRIX); informative statements→triplets (RET-LLM) |
| When to patch working state? | Every turn append STM; on chain-break reset meta (MemoryOS); on MemGPT function calls to working context; on 70% pressure warn then flush |
| How to redact? | Sensory pre-compress drop low-salience tokens (LightMem); secrets→Vault not working prompt (MIRIX); privilege checks before cross-agent read (AIOS) |
| How to rotate buffers? | FIFO STM→MTM (MemoryOS); FIFO queue evict ~50% + recursive summary (MemGPT); LRU-K RAM→disk at 80% (AIOS); KV keep 4 sinks + roll window (StreamingLLM) |

---

## 2. Mechanism cards (P1-focused)

### 2.1 MemoryOS — STM pages + dialogue chain

- **L0 unit:** `page = {Q, R, T, meta_chain}`.
- **Chain write:** LLM checks continuity vs prior pages; if discontinuous, reset chain to current; else summarize whole chain into `meta_chain`.
- **Rotation:** Fixed-length STM queue; **FIFO** oldest page → Mid-Term (out of P1, into P2).
- **Read:** Always inject **all** STM pages into prompt (working context = full short-term).
- **MoDeX:** Implement `Observation` rows append-only; `WorkingState.chain_id` + `chain_summary`; flush to Episode builder on FIFO overflow.

### 2.2 MemGPT — working context + FIFO + pressure

- **Split main context:** (1) read-only system instructions, (2) fixed-size **working context** (RW via functions), (3) **FIFO queue** with recursive summary at slot 0.
- **Pressure:** warn at **~70%** tokens (“memory pressure” system message) so model can copy facts into working/archival **before** eviction; flush at **100%** evicting **~50%**.
- **Self-directed writes:** LLM calls functions; runtime parses/validates; errors returned for retry.
- **MoDeX:** L1 = `WorkingState` document + `RolloutQueue`; daemon emits pressure events; never hard-drop without warn turn.

### 2.3 AIOS — memory manager as kernel service

- **Capture path:** agents do not touch RAM/disk directly — **syscalls** to Memory/Storage managers scheduled FIFO/RR.
- **Working RAM:** per-agent memory block; at **80%** → **LRU-K** swap to disk (Storage Manager / Chroma).
- **Context interrupt:** snapshot/restore LLM generation (text or logits) so working memory ops can preempt long inference.
- **Access:** privilege groups; user confirm on destructive ops.
- **MoDeX:** single-writer queue for L0 append; async swap of cold WorkingState slices; ACL on workstream read.

### 2.4 MemOS — activation vs plaintext working layer

- **Working ≈ activation memory** (KV / hidden / steering); **durable ≈ plaintext MemCubes** with provenance.
- **Scheduler** promotes hot plaintext into activation templates; archives cold.
- **MoDeX:** distinguish ephemeral L1 projection (compiled prompt/KV) from durable L0 log; metadata on every durable write (provenance, type, permissions).

### 2.5 StreamingLLM — KV rolling for infinite sessions

- **Not a fact store** — stabilizes attention when context streams.
- Keep **4 initial sink tokens** + recent window; rebase positions **inside cache**.
- **MoDeX:** when hosting local models, pin system/sink prefix; roll conversation KV independently of L3; still flush facts to MoDeX store (sinks ≠ memory).

### 2.6 SCM — controller-gated working augmentation

- **Flash memory:** always previous turn.
- **Activation memory:** retrieved only if controller says yes.
- Rank `recency + relevance`; k∈[3,10]; summary if item>800 & total>2000.
- **MoDeX:** `should_hydrate(observation) -> bool` before retrieval spend; L1 always includes flash turn.

### 2.7 RET-LLM — structured capture API

- Writes via `[MEM_WRITE{t1>>rel>>t2}]` emitted by model; controller executes.
- **MoDeX:** capture hooks can propose structured ops, but **executor validates** schema before L0/L3 commit (no free-form DB writes from model).

### 2.8 LightMem / MIRIX — cheap front-end filters

- **LightMem:** compress tokens → topic segments → STM buffer until token threshold → summarize → soft-insert (online) / sleep update (offline).
- **MIRIX:** capture every 1.5s, drop similar, batch 20; Core rewrite at 90%.
- **MoDeX:** IDE/hook pipeline: redact → compress → topic-tag → append L0; batch cognify; WorkingState compaction at 90% of budget.

---

## 3. MoDeX P1 implementation recipe

### 3.1 Data structures

```text
Observation (L0): {
  id, workstream_id, ts_event, ts_ingest,
  kind: message|tool|diff|screenshot_digest|system,
  payload_ref,  // pointer; raw blob in object store
  text_digest,  // compressed/redacted
  topic_hint?,
  chain_id?,
  sensitivity: low|medium|high
}

WorkingState (L1): {
  workstream_id,
  persona_blocks: {agent, human},  // MemGPT/MIRIX core
  mission_summary: string,         // HEMA compact ≤ 1–3 sentences
  flash: Observation,              // SCM last turn
  queue: Observation[],            // FIFO rollout
  queue_recursive_summary: string, // MemGPT slot0
  token_budget, tokens_used,
  pinned_prefix_ids[]              // StreamingLLM sinks / system
}
```

### 3.2 Algorithms

**A. Append observation**

1. Redact (`sensitivity=high` → vault path, not prompt).  
2. Optional LightMem retain filter (ratio `r`).  
3. Append L0.  
4. Update L1.flash; push L1.queue.  
5. MemoryOS-style chain: if topic break → new `chain_id` + refresh `mission_summary` snippet.  
6. If `tokens_used ≥ 0.7 * budget` → emit `PressureWarn` (allow model/tools to promote into Anchors).  
7. If `≥ 1.0 * budget` → evict oldest `≈50%` queue non-pinned → append to cognify inbox; recompute `queue_recursive_summary`.  
8. If WorkingState size ≥ `0.9 * capacity` → controlled rewrite of persona/mission (MIRIX), **append-only audit** of prior text.

**B. should_hydrate (SCM gate)**

```text
if chitchat_template(obs): return false
if references_past(obs) or task_continues: return true
default: return false  # minimum necessary memory
```

**C. Swap (AIOS)**

When L1 RAM block ≥ 80%: LRU-K move cold queue items to disk index; keep ids resolvable.

### 3.3 Constants (Phase A defaults)

| Knob | Default |
|------|---------|
| pressure_warn | 0.70 |
| flush_fraction | 0.50 |
| ram_swap | 0.80 |
| core_rewrite | 0.90 |
| sinks_pinned | 4 |
| capture_debounce_ms | 1500 |
| cognify_batch | 20 |
| scm_k | 5 (within 3–10) |
| summary_item_tok | 800 |
| summary_total_tok | 2000 |
| working_prompt_cap | 3500 (small models) |

### 3.4 Anti-patterns

- Using StreamingLLM / long KV **instead of** durable L0.  
- Flat FIFO without working-context promotion (topic mix).  
- Silent in-place overwrite of WorkingState without audit.  
- Always retrieving long-term memory every turn.  
- Putting secrets in WorkingState persona blocks.

### 3.5 Open risks

- Exact α,β,γ heat weights underspecified for STM (heat is MTM — P2).  
- Controller LLM latency vs rule-based `should_hydrate`.  
- Multimodal digest quality vs storage (need redaction classifiers).  
- Cross-workstream leakage without AIOS-style privilege groups (P6).

---

## 4. Acceptance tests (P1)

1. 200-turn session: prompt tokens stay ≤ budget; L0 count = turns; no silent drop without summary.  
2. Pressure warn fires before flush; at least one promotion opportunity turn.  
3. High-sensitivity observation never appears in WorkingState text.  
4. `should_hydrate("tell me a joke") == false`; past-reference true.  
5. After flush, recursive summary non-empty and evicted ids still in L0.

---

*See also: `BATCH_SYSTEMS_AND_EVAL.md` §2.1 and MemGPT/MemoryOS/AIOS cards.*

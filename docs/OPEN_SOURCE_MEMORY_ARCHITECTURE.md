# Open-Source Engineering Memory Architecture

> **Status:** Design lock (conversation synthesis)  
> **Date:** 2026-08-08  
> **Purpose:** Preserve the full product/architecture thinking for a generalized open-source memory + handoff system derived from MoDeX hackathon learnings — without requiring Fivetran, ADK, or BigQuery as core.  
> **Audience:** Future implementers (including future chat sessions) who must not lose context.

---

## 0. Why this document exists

Chat context is ephemeral. This file is the durable constitution for:

1. The **problem** worth solving
2. The **non-goals** (hackathon scaffolding to drop)
3. The **locked stack** (hooks + CLI engine)
4. The **locked store** (compact-native Anchors + Evidence)
5. The **memory graph** orchestration
6. The **handoff** model across sessions, branches, agents, and parallel tasks
7. Research influences and phased build plan

If a future session contradicts this file, update this file deliberately — do not silently drift.

---

## 1. Origin: MoDeX and the genuine problem

### 1.1 What MoDeX was

**MoDeX (Memory of Codex)** — Google Cloud Rapid Agent Hackathon (Fivetran track).

Hackathon stack included:

- Face 1: MCP + IDE hooks capturing coding-agent sessions
- Memory bus: BigQuery + Google Sheet + Fivetran sync
- Face 2: Google ADK multi-agent guide on Cloud Run
- Dashboard UI for judges

### 1.2 The genuine problem (keep)

> **Engineering teams now produce critical reasoning inside AI agent sessions, but that reasoning is never durable, shareable, or loadable the way code is.**

Git stores **what** changed.  
Agent chats store **why** temporarily — then it dies.

Surface symptoms:

1. New agent sessions start cold
2. Teams/agents relitigate rejected approaches
3. “Why did we do it this way?” lives in lost chats
4. Onboarding / context rebuild burns time every session
5. Switching tools (Cursor → Claude Code → Antigravity) resets memory

### 1.3 Problem clusters (priority)

| ID | Problem | Priority |
|----|---------|----------|
| A | Session continuity / handoff | **Core** |
| B | Decision / rejection memory | **Core** |
| C | Cross-tool portability | Important (emerges if store is tool-agnostic) |
| D | Team shared brain / dashboards / lineage | Later |
| E | Full every-prompt surveillance capture | Optional dense mode only |

### 1.4 Crisp problem statement

> AI coding agents now do real engineering work, but their reasoning disappears when the session ends. Teams keep re-deriving context, relitigating settled decisions, and repeating dead ends. Code is versioned; judgment is not. We should make engineering judgment — decisions, rejections, and session context — as durable and handoff-ready as code itself.

### 1.5 Hackathon scaffolding (drop from core product)

| Layer | Why it existed | Needed for OSS core? |
|-------|----------------|----------------------|
| Fivetran connectors / Sheet mirror | Track requirement | No |
| 7 ADK specialists + Guardian theater | Demo platform | No |
| Face 2 Cloud Run chatbot | Judge UX | Later / optional |
| BigQuery as required bus | GCP track | No (overkill for v1) |
| MCP-only capture | IDE story | Adapter later, not sole core |

### 1.6 What MoDeX already got right

- Append-only session events → compress → hydrate
- Deterministic context structuring (not only vague LLM summary)
- Decision / rejected / files-in-flight as first-class ideas
- Handoff CLI seed (`snapshot` / `hydrate` / `status`)
- Insight that **negative knowledge** (rejections) is gold

---

## 2. Product direction: open-source, local-first

### 2.1 Yes — this can be an OSS project

Fits OSS because:

- Universal pain for AI-coding users
- Natural form is local CLI + repo-local store (git-like)
- Inspectable, forkable, offline-friendly
- Apache-friendly posture already present in MoDeX repo lineage

### 2.2 Product wedge (narrow)

Ship one loop first:

> **Capture judgments + working state → compress into durable Anchors → hand off a boot pack so the next session/agent/teammate does not cold-start.**

Do **not** start as “enterprise shared brain + connectors + dashboard.”

### 2.3 Elevator pitch (OSS)

> Open-source memory/handoff layer for AI coding agents. Local-first CLI engine + IDE hooks. Makes decisions, rejections, and session context durable across tools, sessions, branches, and teammates.

---

## 3. Interface theory (what we supervise)

### 3.1 Pipeline

```text
Human / Agent works
        │
        ▼
   CAPTURE  →  STORE  →  COMPRESS/COGNIFY  →  HYDRATE / HANDOFF
```

### 3.2 Doors into the system

| Door | Role | Adoption reality |
|------|------|------------------|
| **CLI engine** | Core runtime + power user / CI / hooks callee | People will not live here daily |
| **IDE hooks** | Default autocapture UX | **Primary adoption path** |
| **MCP** | Agent tool API for read/write | Useful later; unreliable as sole capture |
| **Team handoff bus** | Why capture exists | Packs + shared Anchors |

### 3.3 Supervision layers

**Must supervise (core):**

1. Memory schema (Anchors, Evidence, Episodes)
2. Pack / handoff format
3. Privacy defaults
4. Promotion / invalidation laws
5. Hydrate budget / ranking

**Supervise later (adapters):**

6. MCP tools
7. More IDE hook packs
8. Hosted sync

### 3.4 Learning / build order (historical thinking)

1. CLI + local store
2. snapshot/hydrate loop
3. decision/reject logging
4. MCP wrapper (parked for after hooks+store+handoff)
5. Hooks for automation

**Updated adoption lock:** hooks are default UX; CLI is engine underneath.

---

## 4. Locked runtime architecture

### 4.1 Locked for v1

```text
IDE hooks  ──(auto)──►  CLI engine  ──►  Memory store (Anchors+Evidence+graph)
                              │
                              └── compile HandoffPack → .modex/handoff/...
```

| Piece | Lock |
|-------|------|
| Primary UX | IDE hooks |
| Primary runtime | CLI / library engine |
| Primary team feature | Handoff packs |
| Not primary | Manual CLI habits, MCP-first capture, agent-launcher wrapper |

### 4.2 Why not CLI-only daily UX

- People skip manual logging → holes → trust dies
- People will not relaunch agents through our CLI
- IDEs already own agent lifecycle; hooks are the native sensor

### 4.3 Why not MCP-first capture

- Agents forget to call tools
- Capture becomes probabilistic
- Bad for memory truth

### 4.4 Parked for future

- Full MCP server as main surface
- Agent-launcher CLI wrapper
- Hosted team sync service
- Dense every-prompt default
- Multi-agent orchestration theater
- Neo4j-required infra on day 1

### 4.5 Product spine

```text
capture (hooks) → store (core design) → handoff (why it exists)
```

---

## 5. Capture philosophy (before store lock matured)

### 5.1 Memory ≠ transcript

- Every prompt saved forever ⇒ noisy log
- Judgments + foundations saved well ⇒ memory

### 5.2 Two early memory grades (superseded by Anchor model, still useful)

- **Base memory:** decisions, rejections, constraints, invariants, hard gotchas
- **Detailed / working memory:** current goal, files in flight, recent attempts, rich session context

### 5.3 Meaningful capture triggers

Capture/promote when:

1. Judgment formed (decide / reject / constrain)
2. Base shifted (architecture / invariant change)
3. Work boundary (session end, compact, handoff, PR boundary)
4. Hard lesson (costly gotcha)
5. Intent change (user goal materially changed)

### 5.4 Frequency — rejected as primary UX

Early idea: user profiles `manual | boundary | balanced | dense`.

**Lock update:** remove capture-frequency as primary setting.

ChatGPT-like lesson:

> Observe freely into a short-lived buffer; architecture routes into layers automatically.

Remaining tiny knobs only:

- `privacy: normal | strict`
- `promotion: conservative | normal`

---

## 6. Research synthesis (influences)

### 6.1 Frontier product memory patterns

| System | Pattern adopted |
|--------|-----------------|
| **ChatGPT** | Multi-layer memory, auto distillation, inject small slice at session start |
| **Claude / Anthropic** | Compaction ≠ durable memory; external notes; clear stale tool junk |
| **Claude Code** | Auto memory files, PreCompact externalization, SessionStart inject, load size caps |
| **Grok** | Session window + distilled persistent facts; transparent/editable memories |
| **Cursor rules/memories** | Stable project truth vs generated memories vs handoff files |

### 6.2 Agent memory systems / papers

| System / paper | Pattern adopted |
|----------------|-----------------|
| **MemGPT / Letta** | Hot core vs archival; memory hierarchy like OS RAM/disk |
| **Mem0** | Extract facts; ADD-oriented history; entity linking; fused retrieval |
| **Graphiti / Zep** | Episodes → entities → temporal facts; invalidate don’t delete; provenance |
| **AriGraph** | Semantic graph + episodic vertices linked together |
| **A-MEM** | Atomic notes, write-time linking, memory evolution |
| **HippoRAG** | Entity graph + associative multi-hop retrieval (PageRank-like) |
| **GraphRAG** | Communities + hierarchical summaries |
| **LightRAG** | Dual local/global retrieval; incremental subgraph updates |
| **Generative Agents** | Memory stream, importance, reflection promotion |
| **Nemori** | Event segmentation into episodes; predict/calibrate semantic facts |
| **GAM** | Local episodic graph first; later consolidate to global (avoid contamination) |
| **Cognee** | remember → cognify(graph) → recall |
| **Surveys (2024–2026)** | Working / episodic / semantic / procedural taxonomy; consolidate/retrieve/compress ops |

### 6.3 Critical research lesson for compaction

Compaction of the model context window is **lossy**.

Therefore:

> Anything that must survive must already live outside the transcript in compact structured form **before** destructive compact.

---

## 7. LOCKED STORE ARCHITECTURE

### 7.1 Constitutional lock

> **Durable memory must be compact-native, not compact-rescued.**

Do not store fat transcripts and hope summarization preserves meaning.  
Store memory as already-compact **Anchors** with optional **Evidence**.  
Under compaction pressure, Anchors are guaranteed; Evidence is budgeted.

### 7.2 The memory atom: Anchor + Evidence

#### Anchor (always survive)

Tiny, typed, high-trust memory unit.

```text
Anchor
- id
- kind            # decision | rejection | constraint | gotcha | goal | next_step | open_question
- statement       # one-sentence canonical meaning
- reason          # short why (preferred)
- about[]         # entities: files, libs, services, modules
- status          # active | superseded | disputed | archived
- valid_at
- invalid_at      # null if active
- provenance      # episode_id + actor + source (+ observation refs)
- importance      # 0..1
- repo_scope
- workstream_id?  # null for repo-global anchors
```

**Hard rule:** if context must shrink to almost nothing, Anchors still hydrate.

#### Evidence (budgeted)

```text
Evidence
- id
- supports_anchor_id
- snippet
- source_ref
- weight
```

**Hard rule:** losing Evidence must not erase Anchor meaning.

### 7.3 Compact-pressure survival order (non-negotiable)

When storage/hydrate/context is tight, keep in this order:

1. active constraints  
2. active rejections  
3. active decisions  
4. current goal  
5. next_step  
6. open questions  
7. latest episode digest (short)  
8. evidence snippets  
9. older episodes  
10. raw observations ← first to die  

### 7.4 Store layout (logical)

```text
memory =
  Anchor Store          # canonical, tiny, temporal
+ Evidence Store        # optional, ranked
+ Episode Digests       # medium, compressible
+ Raw Buffer            # ephemeral observations
+ Working State         # mutable per workstream
+ Handoff projections   # compiled boot images
```

### 7.5 Operational rule under compact

```text
on pre_compact / session_stop / handoff:
  1) extract/upsert Anchors from recent work
  2) attach Evidence links
  3) only then allow working-context compact/summary
  4) compile handoff from Anchors first
```

Never reverse this order.

### 7.6 What “good store” means

A second agent, after hydrate, can answer without guessing:

1. What was decided  
2. What was rejected and why  
3. Which files/tasks are hot  
4. What the last human actually asked / next step  

---

## 8. Layered architecture (compatible with Anchor lock)

Layers are projections / pipelines over the store — not a competing product.

```text
L0 RAW OBSERVATION BUFFER     ephemeral high-volume hook events
L1 WORKING STATE              current mission brain per workstream
L2 EPISODE DIGESTS            compressed chapters of work
L3 BASE / ANCHORS             durable team/repo/workstream truth
L4 HANDOFF SURFACE            compiled pack for next consumer
```

### 8.1 L0 Raw buffer

- Always append from hooks (no frequency UX)
- Size/time capped ring buffer
- Not shared by default
- Not “memory” until cognified

### 8.2 L1 Working state

Mutable per workstream:

- goal
- last_user_ask
- files_in_flight[]
- open_questions[]
- blockers[]
- active_branch
- updated_at

Upsert on state deltas only. Keep tiny.

### 8.3 L2 Episodes

Boundary-created digests:

- time_range
- summary
- decisions/rejections touched
- files touched
- failed approaches
- next_steps
- salient evidence (short)

### 8.4 L3 Anchors (base)

Repo/workstream durable facts with temporal invalidation.

### 8.5 L4 Handoff

Projection compiled from ranked Anchors + working + recent episodes.

### 8.6 Automatic routing (replaces frequency)

```text
hook event
  → always L0
  → maybe patch L1 (state-changing)
  → maybe L3 candidate (judgment-grade)
  → on boundary: compact to L2 + promote + compile L4
```

Boundary examples:

- session stop / end
- pre-compact / context pressure
- idle gap
- workstream switch
- explicit `handoff`
- cumulative importance threshold

---

## 9. Memory graph orchestration (core idea detail)

### 9.1 Graph thesis

> Raw logs are not memory. Memory is a graph of linked, graded, time-aware knowledge that can be compacted, retrieved, and handed off.

### 9.2 Cognitive types mapped

| Type | Product meaning | Graph role |
|------|-----------------|------------|
| Working | current goal / hot files / open loops | Working node + hot edges |
| Episodic | what happened in a work chapter | Episode nodes |
| Semantic | durable judgments | Anchor/fact nodes + validity |
| Procedural (later) | reusable playbooks | Skill/pattern nodes |
| Handoff | boot image for next consumer | Compiled projection node |

### 9.3 Node types

```text
EpisodeNode
EntityNode          # file, module, service, library, person, workstream, error-class
AnchorNode          # decision | rejection | constraint | gotcha | goal | next_step | open_question
WorkingNode         # mutable current-state per workstream
CommunityNode       # optional theme cluster (auth, billing, ...)
HandoffNode         # immutable compiled pack version
ObservationRef      # pointer into L0 (not full payload in hot graph)
```

### 9.4 Edge types

```text
MENTIONS            Episode → Entity
DERIVES             Episode → Anchor
RELATES_TO          Entity ↔ Entity
ABOUT               Anchor → Entity
SUPERSEDES          Anchor → Anchor
SUPPORTS            Evidence/Anchor → Anchor
CONTRADICTS         Anchor → Anchor
NEXT_IN             Episode → Episode
IN_WORKSTREAM       * → Workstream
IN_COMMUNITY        Entity/Anchor → Community
COMPILED_INTO       Anchor/Episode/Working → Handoff
LINKED_TO           Anchor ↔ Anchor
CONTINUES           Handoff → Handoff
PARALLEL_WITH       Handoff ↔ Handoff
BRANCH_OF           Handoff → branch facet
```

### 9.5 Temporal model (non-negotiable)

Every semantic Anchor carries:

```text
created_at
valid_at
invalid_at
expired_at?
status: active | superseded | disputed | archived
```

**Law:** contradictions invalidate; they do not erase history.

### 9.6 Provenance model

Every Anchor must answer:

- which Episode produced it?
- which ObservationRefs support it?
- which actor / session / repo / workstream?

No anonymous team truth.

### 9.7 Engineering ontology (prescribed)

Entity types:

```text
Repo, Workstream, Person, AgentTool
File, Module, Service, API, Table
Library, Pattern, ErrorClass, Test
PR, Commit (optional later)
```

Relation examples:

```text
decided_for / rejected_for
depends_on / replaces / conflicts_with
owns / touched / blocked_by
implements / tested_by
```

### 9.8 Four runtime loops

#### Loop A — Encode (hooks, always on)

```text
event → redact → L0 append → cheap entity tags
     → maybe patch WorkingNode
     → importance score
     → maybe mark for link pass
```

#### Loop B — Segment + Cognify (boundary)

```text
1) segment L0 span → Episode
2) extract entities + candidate Anchors
3) write Episode + MENTIONS + DERIVES
4) link to related historical Anchors
5) temporal resolve (invalidate conflicts)
6) optional community update
7) prune L0 span
8) compile HandoffNode
```

#### Loop C — Reflect / Consolidate (less frequent)

```text
when enough episodes accumulate:
  reflect higher-level insights
  promote repeated patterns to Anchors
  evolve old note contexts
  refresh small community summaries
```

#### Loop D — Retrieve + Hydrate

- Default: compile budgeted handoff subgraph
- Later: query-time associative retrieval (entity seeds + neighborhood / PPR-like)

### 9.9 Cognify pseudo-spec

```text
function cognify(episode_span):
  episode = summarize_structured(span)          # deterministic first
  entities = upsert_entities(extract(span))
  candidates = extract_anchor_candidates(span)

  for anchor in candidates:
      related = find_related_anchors(anchor)
      conflicts = detect_conflicts(anchor, related)
      if conflicts:
          invalidate(conflicts, at=anchor.valid_at)
          mark supersession edges
      upsert_anchor(anchor)
      link relationships + ABOUT entities
      link episode DERIVES anchor

  link episode MENTIONS entities
  refresh working node
  compile handoff under budget
  return handoff
```

### 9.10 Failure modes to prevent

- Memory contamination (don’t promote every prompt to Anchor)
- Semantic drift (isolate by workstream/community)
- Catastrophic overwrite (invalidate, don’t delete)
- Context rot on hydrate (strict budget + ranking)
- Orphan facts (require provenance)

### 9.11 Local persistence shape (v1 friendly)

```text
.modex/
  config.json
  raw/events.jsonl
  graph/
    nodes.jsonl
    edges.jsonl
    working/<workstream>.json
  index/
    inverted.json
    vectors.sqlite          # optional later
  handoff/
    <workstream>/current.md
    <workstream>/current.json
    <workstream>/history/<handoff_id>.*
  l3/anchors.jsonl          # or entirely inside graph/nodes
```

No Neo4j required for v1. Embedded graph + jsonl/sqlite is enough.

---

## 10. LOCKED HANDOFF ARCHITECTURE (next primary goal)

### 10.1 Handoff is bigger than cold start

Handoff must cover:

- zero → warm (new agent)
- warm → warmer (continuing sessions)
- across multiple sessions with lineage
- across branches
- across different agent tools
- multiple agents on one machine on different tasks
- multiple agents on same task (parallel)
- relational links between handoffs/sessions

### 10.2 Key abstraction: Workstream

```text
Repo
 └── Workstream (logical task thread, e.g. auth-refactor)
      ├── Session 1
      ├── Session 2
      ├── Parallel session/agent
      ├── Branch tips
      └── Handoff lineage
```

**Agents/sessions are ephemeral. Workstreams are durable.**  
Handoff belongs to a workstream first; branch/session/agent are facets.

### 10.3 HandoffPack schema (conceptual)

```text
HandoffPack
- handoff_id
- schema_version
- repo
- workstream_id
- branch
- session_ids[]
- from_actor
- to_scope                 # self | teammate | any-agent | workstream
- created_at
- parent_handoff_id
- related_handoff_ids[]
- anchors[]                # guaranteed compact memory
- working                  # goal, next_step, files_in_flight, open_questions
- episode_digests[]
- relations
- budget_stats
```

### 10.4 Handoff relation graph

```text
CONTINUES
PARALLEL_WITH
RELATES_TO
BRANCH_OF
SUPERSEDES
DERIVES_FROM_SESSIONS
SAME_WORKSTREAM
MERGES_TO
```

### 10.5 Scenario matrix

| Scenario | Behavior |
|----------|----------|
| Same agent, next day | hydrate latest workstream pack (`CONTINUES`) |
| New agent, same branch | agent-agnostic pack + repo anchors |
| Different branch, same task | branch tip pack + shared workstream/repo anchors |
| Two agents, different tasks | isolated workstreams; share only repo-global anchors |
| Two agents, same task | `PARALLEL_WITH` packs; pin or compose |
| After compact | Anchors intact; pack still coherent |
| Multi-session history | lineage explains evolution; not only latest blob |

### 10.6 Branch rules

- Branch is a facet, not sole identity
- Branch-local working state may differ
- Repo-global active Anchors (especially rejections/constraints) usually transcend branches
- Prefer: current branch tip pack → else workstream latest → always include ranked global Anchors

### 10.7 Multi-agent same machine rules

```text
One active WorkingState per workstream
Multiple workstreams can run in parallel
Never silently mix working states across workstreams
Shared layer = repo-level active Anchors only
```

### 10.8 Parallel same-task rules

- Allow multiple packs under one workstream from different actors
- Relation: `PARALLEL_WITH`
- Compose policy: union Anchors via store supersession truth; working-state conflicts become open_questions / conflict markers

### 10.9 Handoff runtime

```text
boundary (stop | compact | switch | explicit)
  1. externalize Anchors to store
  2. refresh WorkingState for workstream
  3. compile HandoffPack (anchors-first budget)
  4. write current + history
  5. link parent/parallel relations
  6. next consumer uses hydrate router
```

### 10.10 Hydrate router

```text
inputs: repo, branch, actor, files_focus, explicit workstream?

resolve workstream:
  explicit > branch map > file map > last active on machine

select:
  1) repo-global active Anchors (always)
  2) workstream current pack
  3) optional related packs if budget allows

compile injection under hard budget
```

Hydrate modes:

- `latest` (default)
- `since:<handoff_id>`
- `lineage`
- `merge` / `compose`
- `pin:<actor|handoff_id>`

### 10.11 Handoff locked statements

1. Store is Anchor-canonical; handoff is a projection  
2. Workstream is the primary handoff key  
3. Handoffs form a relation graph  
4. Multi-agent safety = workstream isolation + shared repo anchors  
5. Hydrate is routed, ranked, and budgeted — never dump all memory  

### 10.12 One-sentence handoff lock

> Handoff is a workstream-scoped, relational boot pack compiled from compact Anchors — carrying continuity across sessions, branches, and agents, while keeping parallel tasks isolated and linking related sessions instead of flattening them into one blob.

---

## 11. Privacy, trust, and team sharing

### 11.1 Defaults

- Redact secrets/keys/tokens on ingest
- L0 raw never shared by default
- Shareable surfaces: active Anchors + episode digests + handoff packs
- Every Anchor has provenance
- `forget` must work
- Inspectable plain files (markdown/json), not opaque-only storage

### 11.2 Visibility scopes

```text
private_raw
team_graph
handoff
```

### 11.3 Team bus for v1

- `.modex/` in repo or user-accessible project memory dir
- Share via git commit/copy of handoff + anchors
- No hosted sync required initially

---

## 12. CLI surface (engine + human)

### 12.1 Daily / human

```text
modex status
modex handoff
modex hydrate
modex remember decision|reject|constraint "..."
modex forget <id>
modex why <entity|topic>
```

### 12.2 Engine / hooks

```text
modex ingest --from-hook
modex cognify --boundary auto|stop|compact|idle
modex doctor
modex graph export|stats
```

### 12.3 Power

```text
modex graph path <a> <b>
modex graph neighbors <entity> --depth 2
modex hydrate --workstream <id>
modex hydrate --pin <handoff_id>
```

### 12.4 Design rule

All doors (hooks now, MCP later) call the same engine modules:

```text
ingest → router → store/graph → compact/cognify → promote → compile → hydrate
```

---

## 13. Compression philosophy

### Must keep during compaction/cognify

- goal trajectory / current goal
- decisions / rejects / constraints touched
- files that mattered
- failed approaches
- next concrete step
- provenance for new Anchors

### Can drop

- full tool JSON dumps
- trivial edits
- repeated chatter
- raw prompt floods

### Compressor modes

1. **v1:** deterministic structured extraction (default, trustworthy)
2. **later:** optional LLM distill for richer episode summaries

Never make LLM summarizer the only path in v1.

---

## 14. Hydrate budget targets

Inspired by Claude Code load caps:

Target package roughly:

```text
~2–6KB working state
+ top N anchors (constraints/rejects first)
+ 1–3 episode digests
= hard cap (example target: 25–40KB)
```

If over budget, follow §7.3 survival order.

---

## 15. Evaluation criteria

Memory/handoff is working when:

1. Rejection becomes an active Anchor with provenance  
2. Contradicting later judgment supersedes cleanly  
3. Teammate/agent B hydrate includes that Anchor without raw chat  
4. `why <entity>` returns a connected explanation path  
5. Hydrate stays under budget and still enables warm start  
6. L0 rotation does not destroy Anchors  
7. Parallel workstreams do not contaminate each other  
8. Multi-session lineage remains queryable via handoff relations  

Suggested fixture tests:

- handoff continuity Q&A
- no-relitigation test
- budget ceiling test
- loss/rotation test
- contradiction/supersession test
- privacy redaction test
- parallel workstream isolation test

---

## 16. Phased implementation plan

### Phase A — Store skeleton

- `.modex/` layout
- L0 append + rotation
- L1 working upsert
- explicit Anchor remember/forget
- manual handoff compile from Working + Anchors
- CLI: ingest, remember, handoff, hydrate, status

### Phase B — Automatic chapterization

- boundary detector
- L2 episode cognify (deterministic)
- L0 prune after episode
- auto recompile L4

### Phase C — Graph association + promotion

- entity/anchor edges
- conflict invalidation
- ranked hydrate budget
- `modex why`

### Phase D — Hook packs

- major IDE adapters calling same CLI
- SessionStart hydrate inject where supported

### Phase E — later

- MCP tools
- optional LLM episode distill
- associative search / PPR-like retrieval
- hosted sync
- richer community graph

---

## 17. Explicit locks checklist

Use this as the quick constitution:

- [x] Genuine problem = durable engineering judgment + handoff  
- [x] OSS local-first direction  
- [x] v1 UX = IDE hooks; runtime = CLI engine  
- [x] MCP / hosted sync / ADK / Fivetran not core  
- [x] No user-facing capture-frequency matrix  
- [x] Store = compact-native **Anchors + Evidence**  
- [x] Anchors guaranteed under compaction; Evidence budgeted  
- [x] Temporal invalidation (no silent overwrite)  
- [x] Provenance required on Anchors  
- [x] Workstream is primary handoff key  
- [x] Handoffs are relational (lineage/parallel/branch)  
- [x] Multi-agent isolation by workstream  
- [x] Hydrate is ranked + budgeted projection  
- [ ] Next to lock in detail: workstream identity rules + exact JSON schemas + hydrate routing algorithm constants  

---

## 18. Next design locks needed (not done yet)

1. **Exact JSON schemas** for Anchor, Evidence, Episode, WorkingState, HandoffPack, Graph Edge  
2. **Workstream identity algorithm** (how to create/detect/name workstreams)  
3. **Promotion signal list** (what may auto-become an Anchor vs explicit-only)  
4. **Hydrate ranking function + numeric budgets**  
5. **Conflict/compose rules** for parallel packs in one workstream  
6. **IDE hook event minimum set** for v1 (start/stop/pre_compact/prompt/edit)

---

## 19. Canonical summary sentences

**Problem**

> Code is versioned; agent judgment is not.

**Store**

> Under compaction, only Anchors are guaranteed memory; everything else is ranked evidence around them.

**Runtime**

> Hooks observe; CLI engine cognifies; frequency is a property of layers, not a user lifestyle setting.

**Handoff**

> Handoff is a workstream-scoped relational boot pack compiled from compact Anchors for continuity across sessions, branches, and agents.

**Spine**

> `hooks → CLI engine → Anchor store/graph → handoff pack → hydrate`

---

## 20. Glossary

| Term | Meaning |
|------|---------|
| Anchor | Compact durable memory atom (decision/reject/constraint/etc.) |
| Evidence | Optional supporting snippet linked to an Anchor |
| Episode | Compressed chapter of work from a boundary |
| Workstream | Logical task thread that owns continuity |
| HandoffPack | Compiled boot image for next consumer |
| Cognify | Transform raw/working span into graph + anchors + episode |
| Hydrate | Inject ranked memory into a new/continuing session |
| Compact-native | Designed to remain meaningful when storage/context is tight |
| Invalidation | Mark old Anchor inactive when superseded; preserve history |

---

## 21. Document maintenance

When architecture decisions change:

1. Update the relevant section here  
2. Add a short changelog entry below  
3. Do not keep critical locks only in chat  

### Changelog

| Date | Change |
|------|--------|
| 2026-08-08 | Initial synthesis from architecture/design conversation: problem framing, hooks+CLI lock, Anchor store lock, memory graph orchestration, handoff/workstream model, phased plan. |

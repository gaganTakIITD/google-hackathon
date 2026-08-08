# P6 — Privacy, Shareable Anchors & Sealed Packs (Implementation Deep-Read)

> **Pillar:** Inv-Scope, share ladder, `.mxp` seal/hydrate  
> **Sources:** MemClaw, AgentLeak, Collaborative Memory, Miller/Spritely, age/libsodium, MLS, Wormhole, MIRIX Vault, sealed-pack lock.

---

## 1. Inv-Scope (every path)

```python
def require_cap(principal, action, resource_id):
    if not acl.allows(principal, action, resource_id):
        raise NotFound(resource_id)  # not Forbidden
```

Apply to: SQL get-by-id, FTS/vector search, hydrate, MCP tools, export, shared listing.

## 2. Share pipeline
```text
explicit_only:
  redact → kind allowlist → detach evidence → conflict check →
  set shareable=1, visibility=repo_shared_safe → audit → optional reindex shared partition
```

## 3. Seal pipeline (see SEALED_PACKS lock)
Sign-then-encrypt; multi-recipient file key; revoke = reseal epoch++.

## 4. MIRIX Knowledge Vault → MoDeX
Map credentials/API keys/PII to vault semantics: store only in `private_raw`, scanner hard-deny on share/promote.

## 5. Collaborative Memory dual write policies
Separate `π_write_private` vs `π_write_shared`; shared writes may transform/redact. MoDeX share gate is that transform.

## 6. Tables
```sql
CREATE TABLE principals (
  id TEXT PRIMARY KEY,
  display_name TEXT,
  public_key_x25519 TEXT,
  public_key_ed25519 TEXT,
  created_at TEXT
);
CREATE TABLE capabilities (
  id TEXT PRIMARY KEY,
  workstream_id TEXT,
  handoff_id TEXT,
  grantee_principal_id TEXT,
  permissions_json TEXT,
  expires_at TEXT,
  issuer_signature TEXT
);
CREATE TABLE pack_epochs (
  handoff_lineage_id TEXT,
  epoch INTEGER,
  recipients_json TEXT,
  content_hash TEXT,
  path TEXT,
  created_at TEXT,
  PRIMARY KEY (handoff_lineage_id, epoch)
);
```

---

## Changelog
| Date | Change |
|------|--------|
| 2026-08-08 | Initial P6 impl deep-read synthesis. |

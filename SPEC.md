# rapp-eternity/1.0 — The Rappid Identity & Ownership Standard

- **spec_id:** `rapp-eternity/1.0`
- **home:** `kody-w/rapp-eternity/SPEC.md`
- **canonical_home:** `kody-w/rapp-eternity` is the **SINGLE canonical home** of `rapp-eternity/1.0`. Any copy that appears in **RAPP-Bible**, **rapp-egg-hub**, or **rapp-moment** is a **mirror** of this document, never an authority; on any discrepancy, `kody-w/rapp-eternity/SPEC.md` governs.
- **status:** living (locked 2026-06-28)
- **layer:** identity (foundational)
- **supersedes:** the ownership clause of `rapp-moment/SPEC.md` §6 ("a key is an identity"); unifies the record fields of `rapp-rappid-spec/2.0` (rapp-egg-hub)
- **normative for:** `rapp-moment`, `rapp-egg-hub`, `rapp-estate`, `rapp-god`, `rapp-map`, `rapp-resident`, `rapp-commons-event`, and every RAPP component that mints, stores, matches, or transfers a `rappid`

> **One sentence.** A rappid is a permanent, PKI-free content address — the full SHA-256 of an item's canonical content — owned by default through GitHub collaboration and file possession, where a cryptographic keypair is an **optional** sovereignty upgrade that **no component may ever require**.

---

## 0. Scope & Normativity

### 0.1 What this document is

This is **THE** identity standard for the RAPP estate. Anything that has a name and an owner — a Moment, an egg, an agent, a twin, a neighborhood, an estate node — is named by a **rappid** and the rules here govern how that rappid is computed, written, read, matched, owned, and transferred.

**`kody-w/rapp-eternity` is the single canonical home of this standard.** This document at `kody-w/rapp-eternity/SPEC.md` is the one authoritative source of `rapp-eternity/1.0`. Copies hosted in **RAPP-Bible**, **rapp-egg-hub**, **rapp-moment**, or anywhere else are **mirrors** — convenience reflections that MUST track this home and carry no independent authority. Where any mirror disagrees with this document, this document governs.

The keywords **MUST**, **MUST NOT**, **SHOULD**, **MAY**, and **RESERVED** are used per RFC 2119.

### 0.2 What it supersedes and unifies

| Prior text | Disposition under rapp-eternity/1.0 |
|---|---|
| `rapp-moment/SPEC.md` §6 — "the owner's public key fingerprint serves as their identity" | **Retracted as the *default*.** A pubkey is one *optional* binding, never the identity itself. The identity is the content-address hash. Moment MUST re-point to this standard (§7). |
| `rapp-rappid-spec/2.0` (egg-hub) record fields (`pubkey`, `sig_suite`, `birth_attestation`, `key_succession[]`, `registry_anchor`) | **Adopted and unified** as the reserved-but-optional sovereignty fields of the eternity record (§4). The "frozen string / additive record / old readers ignore unknown fields" discipline is **kept verbatim**. |
| Divergent string forms across moment / egg-hub / commons / zoo | **Grandfathered, never rewritten** — read forever via the compatibility contract (§2); only the canonical form is emitted going forward. |

### 0.3 Ground-truth alignment

This spec is consistent with the estate's locked truths: the kernel is the brainstem (the grail); RAPP is a distro pinning it; agents are the only extension; `/chat` is the only wire; **GitHub is the substrate** (raw CDN + Issues-mailbox + PR-consent + Pages-edge); and the north star is "use everyone else's hardware → neighborhoods → estate → metropolis." Identity is the join key that lets that mesh compose without a central server.

---

## 1. The Rappid Is a Content-Address

### 1.1 Definition

A **rappid** is a stable identifier for an addressable RAPP item. Its load-bearing component is a **64-character lowercase hexadecimal SHA-256 digest** — 256 bits, **untruncated** — computed over the item's **canonical content** (§1.3).

```
hash = sha256( canonical_content(item) )      # 64 hex chars, lowercase, never truncated
```

### 1.2 Two iron rules

1. **The hash is the full SHA-256 of content.** It is **never** truncated, never re-encoded to base64/base58 for the canonical form, and never carries an embedded version tag. 256 bits is the eternity contract; collisions are the only thing that can ever break identity, and SHA-256 makes that astronomically unlikely.
2. **The hash is NEVER derived from a public key.** A rappid exists, and is matchable, with **zero** cryptographic material present. PKI is **never load-bearing for existence or matching.** (This is the precise point where `rapp-moment`'s `sha256("moment:"+pk)` derivation is superseded for new mints — see §1.4.)

### 1.3 Canonicalization (the body that gets hashed)

`canonical_content(item)` is produced deterministically so that the same content always yields the same rappid on any machine:

1. Start from the item's record as a JSON object.
2. **Exclude** every ownership/signature/transport field: `sig`, `pub`, `pubkey`, `sig_suite`, `birth_attestation`, `key_succession`, `registry_anchor`, and **every key whose name begins with `_`** (underscore-prefixed keys are non-canonical metadata).
3. Recursively **sort object keys** lexicographically (by Unicode code point).
4. Serialize as **UTF-8 JSON with no insignificant whitespace** (compact separators `,` and `:`), numbers in shortest round-trip form, no trailing newline.
5. SHA-256 the resulting bytes; hex-encode lowercase.

This is the same canonical-body discipline `rapp-moment` already used (sort alphabetically, exclude `sig`/`pub`/underscore keys); rapp-eternity/1.0 makes it the **estate-wide** rule and extends the exclusion set to the unified sovereignty fields. Because every signature/key field is excluded from the body, **adding or removing a key never changes the rappid.** Identity is immutable across the entire optional-sovereignty lifecycle.

### 1.4 Why "content, not key"

Deriving a hash from a public key (the old moment behavior) makes the key **mandatory at birth** — you cannot have an identity without first generating a keypair. That violates the no-mandatory-PKI principle (§5). Under eternity, birth requires only content, so a brand-new item is born identified and ownable with no crypto at all. A keypair, if ever added, binds *to* the already-existing rappid; it does not *create* it.

---

## 2. Canonical String Form + The Read-All-Legacy Compatibility Contract

### 2.1 Canonical string form (EMIT-ONLY)

Every component, going forward, **MUST emit** rappid strings in exactly one form:

```
rappid:@<owner>/<slug>:<64hex>
```

| Part | Rule |
|---|---|
| `rappid:` | literal scheme prefix |
| `@<owner>` | the owner handle — a GitHub login, e.g. `@kody-w` (the `@` is mandatory and literal) |
| `/<slug>` | the immutable human-readable birth slug (the `name_slot`), `[a-z0-9][a-z0-9-]*` |
| `:<64hex>` | the full untruncated SHA-256 content-address from §1 |

**Example:** `rappid:@kody-w/grandma-rose:0d51f2b37c2c4f9a8e5b7f0c92ab4d7e6f1a9c3b8d2e5470a1b9c8d7e6f504132`

The string is **frozen forever.** It carries **no version number** (the spec is versioned; the identifier is not). All evolution happens in the **record** (§4), additively.

### 2.2 The compatibility contract (READ-ALL, FOREVER)

> **Read all legacy forms forever; emit only the canonical form; the hash is the only join key; never rewrite an identity in place.**

A **conforming reader MUST parse and resolve** every historical rappid form below. A conforming writer MUST NOT mint any new identifier except the canonical §2.1 form. When a reader encounters a legacy form, it normalizes it **for display/emission only** and **records the original verbatim** in the non-canonical `_rappid_legacy` field — it **never overwrites** the stored identity, because the stored string is what other parties already reference.

#### Legacy forms a reader MUST accept

| # | Form | Origin | Notes |
|---|---|---|---|
| L1 | `rappid:@owner/slug:64hex` | rapp-moment (current) / canonical | already canonical; passthrough |
| L2 | `rappid:<slug>:<64hex>` | rapp-moment legacy, rapp-egg-hub / rapp-rappid-spec/2.0 | no `@owner`; owner resolved from possession/registry, hash is the join key |
| L3 | `rappid:keeper:<64hex>` | rapp-moment zookeeper | `sha256("keeper:"+pubx)`-derived; key-derived hash is grandfathered, never re-minted that way |
| L4 | `rappid:@owner/slug:32hex` **or** `rappid:slug:32hex` | rapp-egg-hub 128-bit grandfathered | `hash_bits: 128`; legacy UUIDs; accepted on the truncated key, never re-truncated for new mints |
| L5 | `rappid:v3:<base64url(sha256(pubkey))>` | rapp-commons / rapp-resident social layer | a separate sign-everything pubkey lineage; mappable to an eternity record via `_rappid_legacy`, not re-derived |
| L6 | `rappid:dimension:<29hex>` | rapp-commons genesis | truncated genesis ids; accepted verbatim |
| L7 | `rappid:twin:@owner/slug` | rapp-zoo twin variants | hashless display form; resolves to its record's full hash on read |

**Matching rule.** Two records denote the same item **iff their full SHA-256 content-address hashes are equal.** Owner handles, slugs, and string shape are **display sugar**; the 64-hex hash is the sole equality key. For grandfathered truncated forms (L4 32-hex, L6 29-hex), a reader matches on the **stored** truncated key and SHOULD carry `hash_bits` so it never mistakes a 128-bit id for a 256-bit one.

**Never rewrite identity in place.** Migration is **additive annotation**, never mutation (see §7). A reader that "upgrades" a stored L2/L3 string to L1 in someone else's file breaks every existing reference and is **non-conforming.**

---

## 3. Ownership Default — gh-collaborator (sig_suite: "none")

### 3.1 The default trust model

The **default and fully-conformant-forever** ownership model is:

```
ownership = gh-collaborator( @owner ) + file-possession
sig_suite = "none"
```

An item is owned by the GitHub handle in its `owner` field, evidenced by **possession of the file in a repo that handle controls or collaborates on** (push access / PR-consent), atop GitHub's existing identity and access control. This is the substrate doing the work: GitHub *is* the auth.

### 3.2 Rules

1. A record with `owner: "@<github-handle>"`, a content-address `rappid`, and `sig_suite: "none"` is **fully valid and fully conformant — permanently.** `"none"` is not a degraded or transitional state; it is the baseline.
2. **No birth-proof gate may require a signature.** Minting, registering, voting, transferring-by-PR, and resolving MUST all succeed with zero cryptographic material present.
3. **Possession + PR-consent is the transfer mechanism by default.** Ownership moves when the current owner merges a PR (or transfers the repo/file) to the new owner and updates the `owner` field. The merge *is* the consent (Issues-mailbox proposes, PR-consent ratifies).
4. A verifier **MUST accept** a keyless record. Rejecting an item solely for lacking a `pubkey`/`sig` is **non-conforming behavior** (§6).

### 3.3 Why this is sufficient

The substrate already authenticates: only `@kody-w` can push to `kody-w/*` or merge its PRs. Layering a mandatory keypair on top of that buys nothing for the 99% case and taxes every newcomer with key management. Trust = **gh-collaborator + sha256**. Keys are for the case GitHub *cannot* cover (§4).

---

## 4. Keypair Binding — Optional Opt-In Sovereignty

### 4.1 The reserved sovereignty fields

The eternity record reserves these fields. **All MAY be empty/absent**; an empty value is the conformant default. They are **excluded from the canonical body** (§1.3), so populating them never changes the rappid.

| Field | Type | Default | Purpose |
|---|---|---|---|
| `pubkey` | string (JWK or raw) | `""` | Optional public key bound to this rappid |
| `sig_suite` | string | `"none"` | Crypto-agility selector: `"none"` → `"ed25519"` → `"ecdsa-p256"` → post-quantum. Covered by the signature when present. |
| `sig` | string | `""` | Optional signature over the canonical body |
| `birth_attestation` | object \| null | `null` | Optional parent signature at child mint (lineage provenance) |
| `key_succession[]` | array | `[]` | Optional signed rotation/inheritance chain (survives key loss / death) |
| `registry_anchor` | object \| null | `null` | Optional pointer to a registry entry |
| `_rappid_legacy` | string | absent | Non-canonical: the verbatim original string if read in a legacy form |
| `hash_bits` | number | `256` | Self-describing digest width for grandfathered truncated ids |

### 4.2 What sovereignty buys (and only this)

Voluntary keypair binding adds exactly two things GitHub cannot provide:

1. **Takedown survivability** — if a repo is deleted, suspended, or the account is lost, a signed record proves the same owner controls the item on *any other substrate head* (another git host, IPFS, a Pages mirror), because the signature is over the content-address, not the hosting.
2. **Death / succession survivability** — `key_succession[]` lets an owner pre-sign inheritance so identity persists beyond a single account's lifetime.

### 4.3 The hard rule

> **No component may require a keypair for any operation. Ever.**

Birth, registration, resolution, matching, voting, leaderboards, markets, transfer, and federation MUST all function for a `sig_suite: "none"` record. A keypair, when present, is *additional* evidence a verifier MAY weigh (e.g., to rank cross-substrate claims or break a takedown dispute) — never a *gate*. Any spec, agent, or service that makes a key mandatory is in violation of rapp-eternity/1.0 and MUST be corrected.

---

## 5. §3 / §4 Coexistence Proof (no mandatory PKI *and* un-shutdownable)

MASTER_PLAN §3 ("no mandatory PKI") and §4 ("un-shutdownable") appear to conflict — §4 seems to want cryptographic permanence, §3 forbids requiring it. rapp-eternity/1.0 reconciles them:

1. **Un-shutdownability does not come from keys.** It comes from **substrate redundancy + content-addressing**: the same item, identified by the same SHA-256, can be served from raw CDN, Pages-edge, a fork, a mirror, or an offline cache. Delete one head; the content-address still resolves the same item from another. The hash *is* the permanence.
2. **Therefore §4 is satisfied with `sig_suite: "none"`.** A purely gh-collaborator item is already un-shutdownable in the only way that matters: its identity and content survive the loss of any single host because identity is content, and content is replicable.
3. **Sovereignty (§4 keys) is the *optional* extra** for the residual case where you must prove ownership *after* losing the account that hosted it. That is a real but rare need, so it is **opt-in**, satisfying §3.

**Conclusion:** §3 and §4 are not in tension once sovereignty is optional. Un-shutdownability is a property of the *substrate and the hash*; key-sovereignty is a property an owner *may elect* to harden disputes. The two coexist precisely because the keypair is never load-bearing for existence.

---

## 6. Conformance

A **conforming implementation** of rapp-eternity/1.0:

**MUST**
- C1. Compute every new rappid as the full, untruncated, lowercase SHA-256 of the canonicalized content body (§1.3), never derived from a public key.
- C2. Emit rappid strings only in the canonical `rappid:@owner/slug:64hex` form (§2.1).
- C3. Parse and resolve all legacy forms L1–L7 (§2.2), matching solely on the hash, and store the original verbatim without rewriting it in place.
- C4. Treat a record with `owner` + content-address `rappid` + `sig_suite: "none"` and **no key** as fully valid and accept it.
- C5. Perform birth, registration, resolution, voting, and transfer with zero cryptographic material present.
- C6. Treat `pubkey`/`sig`/`birth_attestation`/`key_succession`/`registry_anchor` and all `_`-prefixed keys as non-canonical (excluded from the hash) and additive (old readers ignore unknown fields).

**MUST NOT**
- C7. Truncate, re-version, or re-encode the canonical hash string.
- C8. Require a keypair, signature, or `sig_suite != "none"` for any operation.
- C9. Reject an item solely for lacking a key, or "upgrade"/rewrite another party's stored identity string.

**Explicit retraction.** The framing "a key is an identity" (rapp-moment §6) is retracted. The identity is the content-address; a key is an optional binding to it.

---

## 7. Migration Note (re-pointing moment / egg-hub without breaking minted rappids)

No minted rappid is ever invalidated. Migration is **annotation, not mutation**:

1. **rapp-moment** — Add `spec_ref: "rapp-eternity/1.0"` to records. Existing `rappid:<slug>:<64hex>` and `rappid:keeper:<64hex>` strings remain valid (read as L2/L3). New mints derive the hash from **canonical content (§1.3)**, not `sha256("moment:"+pk)`. The `pub`/`sig` chain becomes the **optional** sovereignty fields of §4; ownership defaults to gh-collaborator. The §6 "pubkey is identity" clause is replaced by a pointer to this document.
2. **rapp-egg-hub** (`rapp-rappid-spec/2.0`) — Already structurally aligned: frozen string, additive record, `sig_suite: "none"`, reserved key fields. It **re-points** by declaring `rapp-eternity/1.0` as the governing identity standard; its `lineage_key`-derived genesis/child hashes are accepted as L2 (content-derived, key-free) and need no change. `hash_bits`/`_migrated_from` self-describing metadata is **required** on grandfathered 128-bit entries to stop drift.
3. **rapp-estate / rapp-god / rapp-map** — Resolve and index on the **hash join key** across all legacy forms; carry `_rappid_legacy` for any non-canonical string; emit canonical on any new write. `rapp-god`'s drift scan adds a check that no public surface requires a key (enforces C8).
4. **Readers never rewrite writers.** When estate-map ingests another party's `rappid:<slug>:<64hex>`, it stores that string verbatim and adds its own canonical display alongside — it does not edit the source. The hash guarantees both forms resolve to the same item.

---

## Worked Example

### A. Birth — keyless, gh-collaborator default

Kody mints a Moment "grandma-rose". The canonical content body (sovereignty/underscore fields excluded, keys sorted, compact UTF-8):

```json
{"created_utc":"2026-06-28T00:00:00Z","kind":"moment","name_slot":"grandma-rose","payload_ref":"moments/grandma-rose.json"}
```

```
sha256(<bytes above>) = 0d51f2b37c2c4f9a8e5b7f0c92ab4d7e6f1a9c3b8d2e5470a1b9c8d7e6f504132
```

The stored eternity record:

```json
{
  "rappid": "rappid:@kody-w/grandma-rose:0d51f2b37c2c4f9a8e5b7f0c92ab4d7e6f1a9c3b8d2e5470a1b9c8d7e6f504132",
  "owner": "@kody-w",
  "name_slot": "grandma-rose",
  "kind": "moment",
  "created_utc": "2026-06-28T00:00:00Z",
  "payload_ref": "moments/grandma-rose.json",

  "sig_suite": "none",
  "pubkey": "",
  "sig": "",
  "birth_attestation": null,
  "key_succession": [],
  "registry_anchor": null,
  "hash_bits": 256
}
```

This record is **fully valid and fully conformant.** Ownership = `@kody-w` controls the file in a `kody-w` repo. No key exists. It is matchable, votable, transferable (by PR), and resolvable — forever.

### B. Reading a legacy reference (no rewrite)

Another party's leaderboard references the same item the old way: `rappid:grandma-rose:0d51f2b3...504132` (form L2). A conforming reader:

- parses it, extracts the hash `0d51f2b3...504132`,
- matches it to the record in (A) **because the hashes are equal**,
- records `"_rappid_legacy": "rappid:grandma-rose:0d51f2b3...504132"` on its **own** index entry,
- emits the canonical `rappid:@kody-w/grandma-rose:0d51f2b3...504132` for display,
- **does not** edit the other party's stored string.

### C. Optional sovereignty upgrade (still the same rappid)

Worried about a future takedown, Kody voluntarily binds an Ed25519 key. He signs the canonical body and fills the reserved fields:

```json
{
  "rappid": "rappid:@kody-w/grandma-rose:0d51f2b3...504132",
  "owner": "@kody-w",
  "sig_suite": "ed25519",
  "pubkey": "ed25519:9f3c…",
  "sig": "MEUCIQ…",
  "key_succession": [],
  ...unchanged content fields...
}
```

The `rappid` is **byte-identical** to (A) — the sovereignty fields are outside the canonical body. Now, if `kody-w/*` is ever taken down, Kody can serve a byte-identical mirror from any other substrate head and the signature proves continuity of ownership over the content-address. A verifier MAY weigh this signature when ranking cross-substrate claims — but a verifier that **required** it would be non-conforming (C8). The keyless record in (A) and the signed record in (C) are the **same eternal identity**.

---

## Appendix — One-paragraph summary for an LLM

A **rappid** is forever: it is the full, untruncated, lowercase **SHA-256 of an item's canonical content** (never of a public key), written as `rappid:@owner/slug:64hex`. Default ownership is **gh-collaborator + file possession** with `sig_suite: "none"`, which is conformant permanently. A keypair is **optional opt-in sovereignty** (takedown/death survivability) that **no component may ever require**. "No mandatory PKI" and "un-shutdownable" coexist because un-shutdownability comes from **substrate redundancy + content-addressing**, not keys. Readers **parse every legacy form forever, match only on the hash, and never rewrite identity in place**; writers **emit only the canonical form**. This document — hosted at its single canonical home `kody-w/rapp-eternity` (RAPP-Bible, rapp-egg-hub, and rapp-moment hold mirrors only) — is the estate's single identity standard and supersedes the "a key is an identity" framing of rapp-moment §6.
# BIP-445 Source-Evidence Research Report

**Status:** READ-ONLY RESEARCH ARTIFACT — NOT A PUBLIC COMMENT

This document records findings from a read-only inspection of `bitcoin/bips` PR #2070 and related sources. It corrects earlier hypotheses and identifies which technical surfaces are supported by current source.

---

## 1. Scope

- Primary PR: `bitcoin/bips#2070`
- PR head: `siv2r:bip-frost-signing`
- PR head SHA: `f7de4568f2497b40bd85bb8fb40da32c05b68d72`
- Fork private dossier: `vitrixLab/bips#1`
- No upstream writes, comments, or PRs were performed.

---

## 2. Methodology

LLM-generated hypotheses were used only as starting points. Each hypothesis was independently verified against normative BIP text, reference implementation, test-vector architecture, and related BIPs/RFCs before being recorded as a finding.

Evidence states used:

- `VERIFIED` — direct source evidence found and checked
- `SUPPORTED` — strong documentary support, not fully independently reproduced
- `UNVERIFIED` — plausible but insufficient evidence
- `UNKNOWN` — evidence insufficient to determine
- `CONFLICT` — sources disagree or source does not support prior claim
- `WITHDRAWN` — prior public/private claim removed after negative evidence

---

## 3. Key Verified Findings

### 3.1 Nonce coefficient `b` is signer-set bound via count prefix

Specification and implementation use:

```text
hash_BIP0445/noncecoef(
  bytes(4, u) ||
  ser_ids ||
  aggnonce ||
  xbytes(Q) ||
  m
)
```

Changelog `0.8.0` explicitly records adding the signer-count prefix to prevent ambiguous `(ids, aggnonce, Q, m)` concatenation.

**Status:** `VERIFIED`

---

### 3.2 Deterministic signing binds `my_id` and signer identifiers

Deterministic nonce includes:

```text
secshare_
my_id
len(ids)
serialize_ids(ids)
aggothernonce
...
```

Changelog `0.4.2` records binding `my_id` and signer IDs.

**Status:** `VERIFIED`

---

### 3.3 Error/blame boundary is explicit

- `ValueError` — malformed setup/config input, such as invalid `SignersContext`.
- `InvalidContributionError` — malicious runtime protocol contributions by signer or coordinator.

**Status:** `VERIFIED`

---

### 3.4 Arbitrary tweaks are explicitly an open security question

Specification distinguishes Bitcoin-derived tweaks from arbitrary adversarial tweaks and states that accepting arbitrary tweaks may negatively affect security. The authors explicitly identify this as an open question.

**Status:** `VERIFIED` as a normative statement. It is not evidence of an actual exploit.

---

### 3.5 BIP-340 challenge is inherited

Challenge uses:

```text
BIP0340/challenge(R.x-only || Q.x-only || msg)
```

This preserves Taproot/BIP340 compatibility.

**Status:** `VERIFIED`

---

### 3.6 ChillDKG interoperability is already tracked

`BlockstreamResearch/bip-frost-dkg#157` explicitly tracks aligning `DKGOutput` with BIP-445 expected input.

**Status:** `VERIFIED` as an existing issue. It is not a novel research discovery.

---

## 4. Withdrawn / Rejected Claims

### 4.1 Absent vs zero-length message semantics

Earlier hypothesis that BIP-445 must distinguish an absent message from a zero-length message is not supported.

Maintainer response: "The message is a mandatory argument and thus always present."

Current source confirms `m` is a mandatory byte-array session parameter. `NonceGen`'s optional `msg` is a separate defense-in-depth input.

**Status:** `WITHDRAWN` / `CONFLICT` with earlier claim.

---

## 5. Evidence Table

| Claim | Source | Status |
|---|---|---|
| Nonce coefficient includes signer count prefix | `bip-0445.md`, implementation | `VERIFIED` |
| Deterministic nonce binds signer subset | implementation, changelog 0.4.2 | `VERIFIED` |
| Serialization ambiguity addressed | changelog 0.8.0 | `VERIFIED` |
| Error/blame semantics specified | implementation | `VERIFIED` |
| Arbitrary tweak security open | spec tweaking section | `VERIFIED` |
| BIP340 challenge inherited | spec + BIP340 | `VERIFIED` |
| ChillDKG alignment tracked | issue #157 | `VERIFIED` / non-novel |
| Absent vs empty message defect | spec + maintainer | `WITHDRAWN` |
| Exact test vector count (68 → 250) | vectors summary | `UNKNOWN` without independent count |
| Coordinator can cause abort | spec | `VERIFIED` |
| Coordinator can forge | spec normative claim | `VERIFIED` as claim |
| BIP327 tweak context reused | spec | `VERIFIED` |

---

## 6. Source Paths Used

```text
bip-0445.md
bip-0445/python/frost_ref/signing.py
bip-0445/python/gen_vectors.py
bip-0445/python/vectors/*
bip-0340.mediawiki
bip-0327.mediawiki
RFC 9591
```

---

## 7. Repository Safety

- `bitcoin/bips`: no writes
- PR #2070: no comments/reviews
- `vitrixLab/bips`: only dossier files updated
- No merge
- No upstream contribution

This report is private research, not a public BIP comment.

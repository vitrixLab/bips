# BIP-445 Technical Assessment

## Documentation Control

| Field | Value |
|---|---|
| Control Number | BIP445-ASSESS-0001 |
| Revision | 2 |
| Branch | assessment/bip-445-technical-review |
| Initial Assessment Commit | c343b6aea204b718dfc856ecce258cdf2723b518 |
| Date | 2026-08-18 |
| Status | INTERNAL DRAFT / NOT FOR UPSTREAM POSTING |

## Version History

| Version | Date | Change Summary |
|---|---:|---|
| 1 | 2026-08-17 | Initial technical assessment checkpoint |
| 2 | 2026-08-18 | Source-evidence update; removed rejected message-semantics claim; added documentation control |

**Assessment:** BIP-445 — FROST Signing Protocol for BIP340 Signatures  
**Repository under assessment:** `vitrixLab/bips`  
**Assessment branch:** `assessment/bip-445-technical-review`  
**Pristine baseline:** `bip-frost-signing`  
**Baseline SHA:** `f7de4568f2497b40bd85bb8fb40da32c05b68d72`  
**Upstream reference:** `siv2r/bips:bip-frost-signing`  
**Assessment status:** Independent technical review; no protocol implementation changes

## 1. Scope and evidence discipline

This record assesses the BIP-445 specification and the implementation state represented by the exact baseline SHA above. The owned `bip-frost-signing` branch remains the pristine reference snapshot. This assessment branch contains review evidence only.

The review is specification-first and implementation-aware. LLM-assisted hypotheses are treated only as hypothesis generation. A candidate claim becomes a current assessment finding only after comparison with normative text, implementation behavior, protocol/test evidence, and relevant prior-art or issue tracking.

Evidence discipline for future findings is:

1. identify the exact normative text;
2. identify the exact implementation/API behavior;
3. provide a reproducible input/output, trace, vector, or test where applicable;
4. distinguish unspecified behavior from an expectation;
5. check whether the point is already discussed or tracked elsewhere;
6. attempt expert-level falsification; and
7. retain only claims that survive that gate.

## 2. Corrected Evidence Status

The latest source-evidence pass produces the following corrections and confirmations:

- **ChillDKG `DKGOutput` alignment:** `VALID AREA, ALREADY TRACKED` in `BlockstreamResearch/bip-frost-dkg#157`. This is a real interoperability boundary, but it is not a novel discovery.
- **Message absent vs zero-length:** `WITHDRAWN`. The message `m` is a mandatory session argument and is always present. The earlier absent-vs-empty claim is not supported and must not be defended or restated as an open specification ambiguity.
- **Public review participation:** `PAUSED`. This follows the maintainer credibility warning and the need to rebuild confidence through independently verified, narrow technical work before further public participation.

## 3. Verified Technical Findings

### 3.1 Nonce coefficient `b` is signer-set bound via a four-byte count prefix

The nonce-coefficient derivation uses the signer-count prefix together with the serialized signer identifiers, aggregate nonce, x-only aggregate public key, and message. The documented changelog records the signer-count prefix as a correction for ambiguous concatenation of the signer-set-bound inputs.

**Status:** `VERIFIED`

### 3.2 Deterministic nonce binds `my_id` and signer identifiers

The deterministic nonce derivation includes the participant's `my_id` and the signer identifiers. The changelog records this binding as an intentional correction to prevent the deterministic nonce from being independent of the active signer context.

**Status:** `VERIFIED`

### 3.3 Error/blame semantics distinguish setup errors from invalid contributions

The implementation distinguishes malformed setup/configuration conditions, represented by `ValueError`, from malicious or invalid runtime protocol contributions, represented by `InvalidContributionError`.

This distinction is useful for implementation behavior and protocol blame semantics; it should not be expanded into stronger attribution claims than the available evidence supports.

**Status:** `VERIFIED`

### 3.4 Arbitrary tweaks are explicitly an open security question

The specification distinguishes the Bitcoin-derived tweak model from arbitrary/adversarial tweaks and explicitly identifies security implications of accepting arbitrary tweaks as an open question. This is a normative qualification of the security boundary, not evidence of an existing exploit.

**Status:** `VERIFIED`

### 3.5 BIP-340 challenge and x-only semantics are inherited intentionally

BIP-445 uses the BIP-340 challenge construction and x-only public-key semantics for the Schnorr signing layer. This is the compatibility surface that connects the threshold signing construction to BIP-340/Taproot semantics.

**Status:** `VERIFIED`

### 3.6 BIP-327 tweak context is reused intentionally

The specification reuses the BIP-327 tweak-context model rather than inventing a separate incompatible tweak representation. This is an intentional dependency/interoperability surface and should be preserved when reasoning about tweak handling.

**Status:** `VERIFIED`

### 3.7 Coordinator abort and forgery boundaries are distinct

Under the stated protocol assumptions, the coordinator can cause an identifiable or protocol-level abort, while the specification's security model does not treat coordinator control alone as sufficient to forge a valid signature. These are distinct capabilities and should not be conflated.

**Status:** `VERIFIED` as a statement of the specified security boundary

### 3.8 ChillDKG interoperability is a real boundary, not a novel discovery

BIP-445 places key generation outside the signing protocol and allows DKG output such as ChillDKG to supply compatible key material. The concrete `DKGOutput`-to-BIP-445 input mapping therefore matters at the API and interoperability boundary. The corresponding ChillDKG issue #157 already tracks alignment work.

**Status:** `VERIFIED` / `NON-NOVEL`

## 4. Withdrawn Claim

### 4.1 Absent vs zero-length message semantics

The earlier assessment treated absent and present-empty message states as a potential specification ambiguity. That position is withdrawn.

The maintainer correction was that the message is a mandatory argument and therefore always present. The current source evidence is consistent with `m` being a mandatory session parameter. An optional `msg` accepted by nonce-generation machinery is a separate defense-in-depth/context input and does not establish an absent-vs-empty defect in the BIP-445 session message semantics.

**Status:** `WITHDRAWN`

**Disposition:** Do not defend, repeat, or present this as an unresolved BIP-445 specification ambiguity.

## 5. Maintainer Feedback Record

The maintainer feedback is recorded as corrective evidence, not as an adversarial characterization.

### Admin 1

- The ChillDKG interoperability point was already tracked.
- The message-semantics point was technically invalid because the message argument is mandatory and always present.

**Classification:** technical correction and novelty correction.

### Admin 2

- Contribution quality was explicitly criticized.
- The feedback included a warning to improve contribution quality or cease posting in the repository.

**Classification:** contribution-quality / credibility warning.

**Assessment response:** Public BIP-445 review participation is paused while the research process is recalibrated around source evidence, falsification, reproducibility, and narrow claims.

## 6. Security and Interoperability Assessment Boundaries

The following boundaries remain important research surfaces:

- signer-set/session binding for deterministic nonce derivation;
- serialization and domain separation for every security-critical hash input;
- the distinction between setup/configuration failures and runtime invalid contributions;
- coordinator capabilities, including abort versus forgery under the stated assumptions;
- the security boundary for arbitrary/adversarial tweaks;
- concrete DKG-output/API compatibility with ChillDKG;
- BIP-340 challenge/x-only compatibility; and
- reuse of BIP-327 tweak-context semantics.

These surfaces should be treated as research targets rather than automatically as defects. Each future claim must pass the evidence gate in Section 1.

## 7. Future Research Roadmap

The broader research roadmap is maintained in the private BIP-445 methodology dossier. The three phases are:

1. **Phase A — Security hardening**: coordinator/threat-model formalization, signer-subset/session-binding regression vectors, serialization/domain-separation analysis, arbitrary-tweak boundaries, and identifiable-abort assumptions.
2. **Phase B — Interoperability/conformance**: cross-implementation harnesses, canonical conformance vectors, C/Rust/C++ API mapping, error/blame semantics, and reference-implementation/pseudocode consistency checks.
3. **Phase C — Ecosystem architecture**: reusable tweak-context abstractions, broader arbitrary/agnostic tweaking research, ROAST/FROST integration guidance, additional implementations, and independent audits.

Reference: [BIP-445 Future Research Roadmap](https://github.com/vitrixLab/bips/blob/research/bip445-methodology-dossier/docs/bip445-research/FUTURE-RESEARCH-ROADMAP.md)

## 8. Evidence / Source References

### Primary specification and implementation baseline

The assessment baseline identifies BIP-445 as a Draft FROST signing specification compatible with BIP-340. It places key generation out of scope, defines signer-context validation, constrains the participant count to `1 <= t <= u <= n < 2^32`, and describes the required relationship between secret shares, public shares, and the threshold public key.

The source-evidence research report records the verified nonce, error, tweak, challenge, and interoperability findings used for this revision.

### External interoperability reference

`BlockstreamResearch/bip-frost-dkg#157` is the existing tracking point for ChillDKG `DKGOutput` alignment. The issue is treated as prior art/tracking evidence, not as a newly discovered defect.

### Security-model qualification

The BIP's stated FROST3/AOMDL security claim is not silently promoted to a broader claim such as full adaptive security. Any stronger security assertion requires an applicable theorem, adversary model, corruption model, assumptions, and evidence that the theorem applies to the concrete BIP-445 construction.

## 9. Current Disposition Table

| Surface | Current disposition | Evidence status |
|---|---|---|
| Signer-set binding in nonce derivation | Confirmed design rule | `VERIFIED` |
| Serialization/domain separation | Confirmed security surface | `VERIFIED` |
| Error/blame boundary | Confirmed implementation distinction | `VERIFIED` |
| Arbitrary tweak security | Explicit open question | `VERIFIED` as normative qualification |
| BIP-340 challenge/x-only semantics | Intentional compatibility surface | `VERIFIED` |
| BIP-327 tweak context | Intentional reuse | `VERIFIED` |
| Coordinator abort vs forgery | Distinct stated capability boundary | `VERIFIED` |
| ChillDKG `DKGOutput` alignment | Valid, already tracked | `VERIFIED` / `NON-NOVEL` |
| Absent-vs-empty message claim | Withdrawn | `WITHDRAWN` |
| Public review participation | Paused for credibility/process recalibration | `CURRENT` |

These dispositions are assessment findings, not proposed BIP modifications.

## 10. Conclusion

This assessment remains an independent technical-review artifact. It does not propose modifying or merging BIP-445 protocol text or implementation code.

The latest evidence pass narrows the assessment rather than expanding it: signer-set binding, serialization/domain separation, error/blame boundaries, tweak security, BIP-340/BIP-327 compatibility, coordinator capability boundaries, and concrete ChillDKG interoperability remain legitimate research surfaces. The absent-vs-zero-length message claim is withdrawn, and the ChillDKG point is explicitly recognized as already tracked.

Future public participation, if reconsidered, should be limited to claims that can survive exact normative citation, implementation inspection, reproducible evidence, prior-art checking, and direct falsification attempts.

**Audit integrity:** This file is an assessment artifact only. It does not modify BIP-445 protocol text, implementation code, or the pristine `bip-frost-signing` branch.

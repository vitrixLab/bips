# BIP-445 Review Findings

This file separates technically useful observations from claims that were rejected, already tracked, or remain questions. Status is intentionally conservative.

| Finding | Evidence status | Research record |
|---|---|---|
| **Coordinator trust boundaries:** identifiable aborts may depend on coordinator honesty. | `SUPPORTED` | Supported by the review discussion. The issue is retained as a threat-model/design question rather than stated as a demonstrated protocol failure. |
| **Signer-set binding:** deterministic nonce generation must bind to the active signer subset to prevent cross-session secret recovery. | `CURRENT` | The point was fixed by the PR author and is retained here as a design rule established through the review process. |
| **Serialization/domain separation:** noncecoef concatenation had an ambiguity risk and was fixed by adding a signer-count prefix. | `VERIFIED` | Recorded as a concrete discussion fact: the serialization concern was real and the author changed the construction. |
| **Arbitrary tweaks:** attacker-supplied tweaks raise an open security question; the statement needs a bounded security boundary. | `CURRENT` | Adopted as a clarification in the PR. The research position is to avoid overgeneralizing the security guarantee beyond the specified tweak model. |
| **Interoperability boundary with ChillDKG:** DKGOutput alignment is relevant to interoperability. | `SUPPORTED` | Valid interoperability observation, but already tracked in `BlockstreamResearch/bip-frost-dkg#157`; therefore non-novel rather than a new upstream finding. |
| **Message semantics:** an absent-vs-zero-length message distinction was proposed. | `OBSOLETE` | Withdrawn after maintainer correction. The message argument is mandatory and always present, so the proposed absent-message distinction does not apply to the reviewed interface. |
| **Error/blame semantics:** distinguish setup errors from malicious runtime contributions. | `SUPPORTED` | Retained as design guidance for specification and implementation clarity; it should be tied to concrete failure paths before becoming a normative criticism. |
| **Test-vector expansion:** coverage increased from 68 to 250 cases. | `VERIFIED` | Factual observation from the review history. The remaining research question is whether the vectors provide structured conformance coverage across all relevant dimensions. |

## Interpretation notes

### Coordinator trust boundaries

The useful research question is not simply whether a coordinator can cause an identifiable abort. The stronger question is which assumptions the protocol makes about coordinator behavior, what participants can detect, and which failure modes are attributable to coordination versus participant inputs.

### Signer-set binding

The corrected design rule is that deterministic nonce material must be bound to the actual signer subset/session context relevant to the signing transcript. The point is retained as a security invariant, not as an unresolved criticism.

### Serialization and domain separation

The noncecoef concatenation issue demonstrates why hash-input structure should be audited explicitly. Any concatenation that can admit ambiguous decompositions should be represented with explicit framing, length, count, or another unambiguous domain-separation mechanism.

### Arbitrary tweaks

The appropriate future question is the exact security boundary of attacker-supplied tweaks. The dossier does not assert that arbitrary tweaking is universally insecure or universally safe. It records the need for a bounded statement tied to the actual construction and threat model.

### ChillDKG interoperability

The DKGOutput alignment point is retained because interoperability is important, while explicitly recording that it is already tracked elsewhere. The correct lesson is that a valid observation may still be non-novel.

### Message semantics

The absent-versus-empty-message hypothesis is intentionally retained only as a failure-analysis example. It is not a current finding. The mandatory-message-interface correction supersedes the earlier inference.

### Error and blame semantics

A useful specification should make clear which failures occur during setup and which can arise from malicious or invalid runtime contributions. This can improve implementation behavior and test design without necessarily constituting a protocol defect.

### Test vectors

The increase from 68 to 250 cases is useful evidence of expanded testing. A separate conformance matrix is still warranted so coverage can be evaluated by property, signer-set cardinality, transcript state, serialization boundary, tweak class, and expected failure mode rather than by raw vector count alone.

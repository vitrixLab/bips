# Future BIP-445 Research Roadmap

## Phase A — Security hardening

### A1. Formal coordinator/threat-model section

Document the coordinator's capabilities, honest-but-curious versus malicious behavior, participant assumptions, identifiable-abort conditions, and the security consequences of coordinator manipulation.

### A2. Signer-subset/session-binding regression vectors

Create permanent regression vectors covering signer-set changes, session separation, deterministic nonce derivation, and cross-session transcript binding. The objective is to make the signer-subset invariant executable rather than merely descriptive.

### A3. Serialization/domain-separation table

Build a table for every hash input and transcript component showing its fields, ordering, framing, length/count encoding, and domain-separation purpose. Use it to detect ambiguous concatenations systematically.

### A4. Explicit arbitrary-tweak security boundary

State exactly which tweak classes are covered by the construction and which attacker-supplied or arbitrary tweak scenarios require separate analysis. Avoid claims broader than the demonstrated model.

### A5. Formal identifiable-abort assumptions

Specify when an abort is attributable to the coordinator, a signer, malformed input, or an unavoidable protocol condition, and what participants can actually identify from the available evidence.

## Phase B — Interoperability

### B1. Cross-implementation test harness

Build a harness that compares independent implementations over identical transcripts, signer subsets, messages, tweaks, failures, and serialization boundaries.

### B2. Canonical conformance vectors

Define a canonical set of positive, negative, boundary, and adversarial vectors. Coverage should be organized by protocol property rather than only total vector count.

### B3. C/Rust/C++ API mapping guidance

Map the specification's conceptual objects and operations onto representative C, Rust, and C++ APIs, documenting ownership, serialization, error handling, and type-boundary assumptions.

### B4. Error/blame semantics specification

Develop explicit guidance separating setup/configuration failures from malicious or invalid runtime contributions, including how an implementation should expose or suppress blame information.

### B5. Reference implementation ↔ pseudocode consistency checks

Systematically compare pseudocode, normative prose, reference implementation, and tests so that semantic drift is detected before it becomes an interoperability issue.

## Phase C — Ecosystem architecture

### C1. Reusable tweak-context abstraction

Research whether Bitcoin signing BIPs can share a common tweak-context abstraction that preserves explicit domain separation, signer/session binding, and clearly scoped threat assumptions.

### C2. Arbitrary/agnostic tweaking research

Investigate broader arbitrary or agnostic tweaking models without assuming that results from one construction transfer automatically to another.

### C3. Expanded ROAST/FROST integration guidance

Map the relevant interaction points among BIP-445, FROST, ROAST, DKG outputs, and signing-session state. Focus on concrete interface and transcript compatibility rather than conceptual similarity alone.

### C4. Additional implementations and independent audits

Seek independent implementations and audit perspectives before making strong ecosystem-level security or interoperability claims.

## Research gate for all phases

Each completed phase should produce evidence that can survive the same public-comment gate:

- exact specification reference;
- implementation/API reference;
- reproducible trace, vector, or test;
- prior-art/tracking check;
- explicit falsification attempt; and
- clear evidence state.

The roadmap therefore treats documentation quality and verification discipline as part of the technical work, not as a separate editorial step.

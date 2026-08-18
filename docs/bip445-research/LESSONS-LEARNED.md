# Lessons Learned

## 1. LLM output is a hypothesis generator, not protocol evidence

LLM-assisted review is useful for finding candidate questions, edge cases, and relationships across documents. It is not sufficient evidence for a public protocol criticism. Every candidate claim requires independent verification against the normative specification, implementation/API behavior, and a concrete reproducible trace or vector.

## 2. Review volume does not substitute for technical depth

A large number of comments can create noise without increasing review quality. Future review should prioritize a few technically substantial findings over broad issue enumeration.

## 3. Withdrawn claims should not be defended

Once a claim is contradicted by the actual interface or implementation, the correct action is to withdraw it and record why it failed. Continuing to defend a falsified hypothesis weakens both the review and the evidence process.

## 4. A non-novel point is not necessarily a wrong point

The ChillDKG interoperability observation was valid as an interoperability concern, but it was already tracked in `BlockstreamResearch/bip-frost-dkg#157`. Future review must distinguish **incorrect**, **already tracked**, **already fixed**, and **newly demonstrated**.

## 5. Verify exact API semantics before semantic critique

The absent-versus-empty-message proposal failed because the message argument is mandatory and always present. Before constructing a semantic distinction, identify the precise function signature, normative argument requirements, and implementation path.

## 6. Future public participation should clear a higher evidence bar

A future public comment should be grounded in one extremely hard-to-dismiss technical point: exact specification text, exact implementation behavior, a minimal reproduction or vector, and an explanation of why existing tracking or documentation does not already resolve it.

## 7. Research state must remain explicit

The dossier uses evidence states so that an interesting hypothesis cannot silently become a finding. `UNVERIFIED`, `UNKNOWN`, and `CONFLICT` are legitimate research states and should remain visible until the evidence changes.

## 8. The practical operating model

The resulting working model is:

```text
Generate broadly with LLM assistance
        ↓
Narrow aggressively through falsification
        ↓
Verify specification + implementation + trace
        ↓
Check prior art / existing tracking
        ↓
Publish only durable findings
```

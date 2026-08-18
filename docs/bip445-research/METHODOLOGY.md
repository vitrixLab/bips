# Corrected BIP-445 Research Methodology

## Core rule

LLM-assisted hypothesis generation must be separated from independent technical verification:

```text
LLM-assisted hypothesis generation
        ↓
Independent verification against normative text, implementation, and protocol traces
        ↓
Only verified findings become public comments
```

The purpose of this separation is not to prohibit LLM assistance. It is to prevent plausible language-model inferences from being promoted directly into claims about a protocol's security, API semantics, interoperability, or correctness.

## Public-comment gate

For every proposed public BIP-445 comment, answer all of the following:

1. **Exact normative target:** What exact normative text is being challenged?
2. **Implementation/API evidence:** What exact implementation or API behavior establishes the claimed problem?
3. **Concrete trace:** What concrete input/output pair, transcript, test vector, or protocol trace demonstrates it?
4. **Specification status:** Is the behavior genuinely unspecified, or is the criticism only an expectation that is not stated by the specification?
5. **Existing work:** Has the matter already been discussed, fixed, or tracked elsewhere?
6. **Expert falsifiability:** Could an expert familiar with the code immediately falsify the claim?
7. **Minimal reproduction:** Can a minimal reproducible example be supplied?

If any answer is missing or weak, the item remains **private research** and is not posted as public protocol criticism.

## Observation versus inference

Review-derived observations and independent inferences must remain distinct.

- A **review-derived observation** is directly grounded in an observed specification passage, implementation behavior, test vector, or existing discussion.
- An **independent inference** is a hypothesis derived from those materials but requiring additional verification.

An inference can be useful research even when it is ultimately false. The error occurs when the inference is represented publicly as though it were already established.

## Evidence state vocabulary

| State | Meaning |
|---|---|
| `VERIFIED` | Directly established by normative text, implementation, and/or a reproducible technical trace sufficient for the claim. |
| `SUPPORTED` | Strongly supported by authoritative discussion or evidence, but not necessarily independently reproduced in full. |
| `UNVERIFIED` | Plausible hypothesis lacking sufficient evidence. Remains private. |
| `UNKNOWN` | Available evidence is insufficient to determine the claim. |
| `CONFLICT` | Authoritative sources or observations disagree; investigation is required. |
| `OBSOLETE` | The claim may once have been applicable but no longer reflects the current design or implementation. |
| `CURRENT` | The statement reflects the currently observed design state after verification; it is not itself a substitute for evidence. |

These states must not be collapsed into one another.

> **UNVERIFIED ≠ VERIFIED**
>
> **UNKNOWN ≠ FAILURE**
>
> **CONFLICT ≠ SUCCESS**

## Verification sequence

### 1. Read the normative specification

Quote or identify the exact text that allegedly creates the issue. Determine whether the disputed behavior is mandatory, optional, undefined, or simply absent from the document.

### 2. Inspect implementation/API behavior

Map the specification statement to the relevant function, argument, data structure, serialization routine, or error path. Record what the implementation actually does rather than what it appears likely to do.

### 3. Reproduce the behavior

Use the smallest concrete example capable of distinguishing the proposed failure mode from normal behavior. Prefer deterministic vectors, transcripts, or tests over prose reasoning.

### 4. Search existing discussion and tracking

Determine whether the point is already fixed, clarified, accepted as a design trade-off, or tracked in another project or issue. A non-novel observation can still be useful research, but it should not be represented as a new discovery.

### 5. Attempt falsification

Actively try to disprove the candidate claim. In particular, ask what a maintainer or implementation expert would point to as the shortest counterexample.

### 6. Decide publication status

Only a claim that survives the preceding checks should become a public review comment. Otherwise retain it as a private research item with its current evidence state and the missing verification step recorded.

## Review-quality objective

The objective is not to maximize the number of comments. The objective is to produce a small number of technically durable observations that are difficult to dismiss because the specification text, implementation behavior, and concrete evidence all align.

A future public review should therefore optimize for **depth, reproducibility, and falsifiability**, rather than comment volume.

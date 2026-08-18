# Methodology

## Quantitative phase

The quantitative phase targets:

- file count
- extension distribution
- line count
- word count
- section count where detectable
- reference/citation count where detectable

For this inventory, the repository tree and README index establish the canonical file count and extension distribution. Exact per-document line, word, section, and reference counts are marked `UNKNOWN` because they were not independently derived from each document body in this pass.

## Qualitative phase

The qualitative phase classifies each indexed BIP by:

- **BIP type:** Standards / Informational / Process
- **Status:** the status published by the fork's README index where present, including Draft, Proposed, Rejected, Final, Active, Replaced, Withdrawn, Deferred, and Obsolete
- **Technical family:** consensus, P2P, wallet, signing/cryptography, scripting, mining, process/governance, or other
- **Dependency relationships:** requires, references, and related BIPs where directly established

The technical-family labels are research classifications derived primarily from the published layer plus title semantics. They are not normative classifications made by the BIP authors.

## Research-relevance rubric

Research relevance is a triage classification for deciding where deeper research is most likely to be useful:

- **HIGH:** consensus, cryptographic/signing, or security-critical protocol primitives with broad dependency or interoperability surfaces
- **MEDIUM:** wallet, P2P, scripting, mining, or other implementation-facing protocol surfaces
- **LOW:** process/governance and historical or narrowly informational material

This rubric is a research-indexing rule, not a claim about importance to the Bitcoin ecosystem.

## Evidence rules

- A quantitative metric that cannot be derived directly is `UNKNOWN`.
- A qualitative tag is a classification, not a normative claim.
- Repository-index metadata is not silently substituted for document-body evidence when the requested metric requires the document body.
- No public comment, upstream issue, review, branch, or contribution is authorized by this inventory.

# BIP-445 Technical Assessment Record

**Assessment:** BIP-445 — FROST Signing Protocol for BIP340 Signatures  
**Repository under assessment:** `vitrixLab/bips`  
**Assessment branch:** `assessment/bip-445-technical-review`  
**Pristine baseline:** `bip-frost-signing`  
**Baseline SHA:** `f7de4568f2497b40bd85bb8fb40da32c05b68d72`  
**Upstream reference:** `siv2r/bips:bip-frost-signing`  
**Assessment status:** Independent technical review; no protocol implementation changes

## 1. Scope and exact baseline

This record assesses the BIP-445 specification and the implementation state represented by the exact baseline SHA above. The owned `bip-frost-signing` branch remains pristine. This assessment branch contains review evidence only.

The BIP identifies itself as a Draft specification for a BIP340-compatible FROST protocol and states that key generation is out of scope. It describes trusted-dealer setup and DKG such as ChillDKG as compatible sources of key material. The specification also defines a participant bound of `n < 2^32` and explains that this follows from four-byte participant-identifier encoding.

## 2. Methodology

The review is specification-first and implementation-aware:

1. Freeze the exact upstream-derived baseline by commit SHA.
2. Separate specification claims from implementation behavior and from external cryptographic security results.
3. Trace security-critical data through the Signers Context, key material, nonce binding, message binding, and partial-signature verification paths.
4. Check stated contracts against the representations and validation rules required by the protocol.
5. Record findings without modifying BIP-445 protocol text or implementation code.
6. Treat claims that require an external security proof, DKG contract, or implementation-level test as claims requiring explicit evidence rather than silently accepting them.

## 3. Finding A — Adaptive-security model

**Disposition:** Requires security-model qualification / further proof review.

The BIP states that FROST3 has an existential-unforgeability result under the AOMDL assumption and describes protection against a malicious coordinator, including a coordinator colluding with up to `t-1` signers. Those statements establish important security goals, but the BIP text does not by itself define a full adaptive-corruption security experiment or provide a proof of full adaptive security for the concrete BIP-445 construction.

This distinction matters because adaptive security allows corruption decisions to depend on information learned during protocol execution. Contemporary FROST literature treats adaptive security as a separate and materially stronger security question, with additional assumptions and proof considerations. Therefore, the assessment should not interpret the BIP's AOMDL/FROST3 statement as automatically establishing full adaptive security.

**Assessment consequence:** Any implementation or deployment claim of full adaptive security must identify the exact security theorem, adversary model, corruption threshold, assumptions, and applicability to the BIP-445 construction.

## 4. Finding B — ChillDKG / DKGOutput compatibility contract

**Disposition:** Compatibility contract requires explicit verification.

BIP-445 says that key generation is out of scope and that implementations may use ChillDKG. It further says valid key material must satisfy both of these conditions:

1. each secret share is a Shamir share of the threshold secret key; and
2. each public share equals `secshare * G`.

It then states that ChillDKG satisfies these conditions and that its DKG output can be used directly as key material.

The assessment requirement is therefore not merely that a library is called `ChillDKG`, but that the concrete DKG output supplied to the BIP-445 signing API maps exactly to the BIP's `secshare`, `pubshare`, threshold-public-key, participant-identifier, `n`, and `t` semantics, including the required Taproot-safe threshold public-key property.

**Assessment consequence:** A compatible implementation should document and test this field-level contract rather than relying on name-level compatibility.

## 5. Finding C — ValidateSignersCtx semantics

**Disposition:** Security-critical validation contract; requires explicit implementation correspondence.

BIP-445 requires the Signers Context to pass `ValidateSignersCtx` before signing. The stated validation rejects duplicate identifiers and confirms that the key material reproduces the threshold public key. The specification also says that `Sign`, `PartialSigVerify`, and `PartialSigAgg` include this check for clarity, while an implementation may validate once and reuse the validated context.

This makes the validation boundary security-critical. In particular, the review must distinguish:

- validation of the context's internal consistency;
- validation that the selected signer set is the intended signer set;
- protection against coordinator equivocation over different Signers Context values; and
- validation of the underlying key-generation protocol itself.

The BIP explicitly warns that passing `ValidateSignersCtx` establishes functional compatibility with the signing protocol but does not establish security of the key-generation protocol.

**Assessment consequence:** Implementations must not treat `ValidateSignersCtx` as a substitute for authenticating the signer context or for proving the security properties of the DKG/setup mechanism.

## 6. Finding D — Absent vs present-empty message semantics

**Disposition:** Requires implementation-level clarification and test coverage.

BIP-445 makes message handling an optional feature and permits an implementation to fail for unsupported message lengths. The nonce-generation interface also uses optional contextual inputs, including the message where it is already determined.

The assessment must distinguish three states where an API permits optional data:

- the message/context argument is absent;
- the argument is present and contains an empty byte string; and
- the argument is present and contains a non-empty message.

These states must not be conflated if serialization or hashing treats absence differently from an encoded empty value. Because nonce derivation is security-sensitive, the canonical serialization of optional inputs must be deterministic and identical across independent implementations.

**Assessment consequence:** Add explicit vectors/tests for absent, present-empty, and ordinary message values wherever the API exposes optional message/context fields. The protocol specification should define the serialization distinction if the distinction is security-relevant.

## 7. Finding E — `n < 2^32` validation

**Disposition:** Specification-level validation requirement; implementation enforcement should be verified.

BIP-445 explicitly constrains the participant count to `1 <= t <= u <= n < 2^32`. The accompanying rationale states that participant identifiers are serialized as four-byte big-endian integers and fed into the tagged hash that binds nonces to the signer set, so identifiers must fit in 32 bits.

This is not merely a practical recommendation. It follows from the specified identifier encoding and therefore needs an explicit boundary check before serialization. An implementation must reject an `n` or participant identifier that cannot be represented by the required four-byte encoding rather than silently truncating, wrapping, or accepting an out-of-range value.

**Assessment consequence:** Boundary tests should cover the largest valid value and the first invalid value, including identifier serialization at the `2^32 - 1` / `2^32` boundary. The assessment should also verify that all relevant APIs enforce the same bound consistently.

## 8. Evidence / source references

### Primary specification evidence

The assessment baseline's `bip-0445.md` states:

- BIP-445 is a Draft specification for FROST compatible with BIP340.
- Key generation is out of scope and trusted-dealer setup or a DKG such as ChillDKG may supply key material.
- Valid key material requires Shamir-consistent secret shares and public shares equal to `secshare * G`.
- `ValidateSignersCtx` rejects duplicate identifiers and checks reproduction of the threshold public key.
- Passing `ValidateSignersCtx` does not establish security of the key-generation protocol.
- The participant count is constrained by `1 <= t <= u <= n < 2^32`, with the four-byte identifier encoding given as the reason for the upper bound.

### External cryptographic context

The adaptive-security finding is deliberately separated from the BIP's own FROST3/AOMDL statement. Adaptive security is a distinct security property and must be evaluated against the exact construction and security model rather than inferred from the existence of an existential-unforgeability result.

### Author-supplied design analysis

A publicly available analysis by the BIP author discusses the flow of `SignersContext`, coordinator equivocation, and related validation/security questions. This is useful corroborating material, but it is treated as secondary evidence rather than as authoritative proof of the protocol's security.

## 9. Severity and disposition

| Finding | Current disposition | Severity basis |
|---|---|---|
| A — Adaptive-security model | Qualification / proof review required | Security-model claim can be overstated if adaptive security is inferred without a matching theorem |
| B — ChillDKG/DKGOutput contract | Compatibility verification required | Incorrect field/representation mapping can invalidate signing or security assumptions |
| C — ValidateSignersCtx semantics | Security-critical validation review | Context integrity/authentication is distinct from DKG security |
| D — Absent vs present-empty message | Clarification + vectors required | Ambiguous optional-input serialization can create cross-implementation divergence |
| E — `n < 2^32` | Boundary validation required | Identifier encoding has a hard representational bound |

These dispositions are assessment findings, not proposed BIP modifications.

## 10. Conclusion / merge recommendation

This record does **not** propose merging protocol changes into the BIP-445 baseline. The pristine implementation branch remains the reference snapshot.

The current assessment conclusion is:

- BIP-445 contains explicit contracts for signer-context validation, key-material compatibility, message optionality, and the `n < 2^32` participant bound.
- Those contracts should be made executable through implementation tests and interoperability vectors before treating them as fully demonstrated properties.
- The adaptive-security claim requires particular care: the specification's FROST3/AOMDL statement should not be silently promoted to a claim of full adaptive security without an applicable security theorem.
- ChillDKG compatibility should be demonstrated at the concrete DKG-output/API boundary.
- `ValidateSignersCtx` must remain conceptually separate from authentication of the signer context and from the security of the DKG.
- Optional message semantics and the four-byte participant-identifier boundary deserve explicit negative and boundary tests.

**Merge recommendation for this assessment record:** retain as an independent technical-review artifact. Any proposed protocol/specification changes should be raised separately and must not be conflated with this assessment branch.

---

**Audit integrity:** This file is an assessment artifact only. It does not modify BIP-445 protocol text, implementation code, or the pristine `bip-frost-signing` branch.

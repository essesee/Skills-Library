# Evaluation Framework

Domain-agnostic evaluation pass definitions. Each pass gets specialized by the domain profile content loaded in Phase 1.

## Severity Ratings

| Severity | Criteria | Action Required |
|----------|----------|-----------------|
| **Critical** | Fundamentally wrong for the industry. Would cause regulatory issues, data loss, or business failure. | Must fix before proceeding. Stop and surface to user. |
| **High** | Significant domain mismatch. Violates established industry patterns or misses key requirements. | Should fix. Surface to user for decision. |
| **Medium** | Suboptimal for the domain. Works but misses industry best practices or creates unnecessary friction. | Recommend fixing. Include in findings. |
| **Advisory** | Opportunity to better align with domain conventions. Nice-to-have improvement. | Note for consideration. |

## Finding Format

Each finding must include:

```
**[Title]** — [Severity]
Context: What was observed in the artifact
Domain Rationale: Why this matters in this industry (cite domain profile section)
Recommended Action: Specific fix or improvement
```

---

## Pass 1: Domain Fit

**Question:** Does this artifact align with how this industry actually works?

**Evaluate against domain profile sections:** Industry Overview, Core Domain Concepts

**Look for:**
- Core concepts that are missing, misnamed, or misunderstood
- Business processes that don't match how the industry operates
- Assumptions that contradict industry reality
- Missing entities or relationships that are fundamental to the domain
- Terminology that doesn't match industry standard language

**Severity guide:**
- Critical: Core domain concept is wrong or missing (e.g., a travel booking system with no concept of cancellation)
- High: Important concept is misrepresented or oversimplified
- Medium: Terminology drift from industry standard
- Advisory: Minor naming convention differences

## Pass 2: Industry Pattern Conformance

**Question:** Does this follow established patterns in the industry, or reinvent the wheel?

**Evaluate against domain profile sections:** Established Patterns

**Look for:**
- Standard workflows that are being reimplemented differently without justification
- Industry-standard data formats or protocols being ignored
- Architectural patterns common in this industry that aren't being followed
- Opportunities to leverage existing industry solutions instead of building custom
- Patterns that work in other industries but fail in this one

**Severity guide:**
- Critical: Ignoring a mandatory industry standard (e.g., not using IATA codes in airline systems)
- High: Reinventing a solved problem with a worse solution
- Medium: Deviating from common patterns without clear benefit
- Advisory: Alternative approach that works but isn't conventional

## Pass 3: Regulatory & Compliance

**Question:** Are there industry-specific compliance, legal, or regulatory considerations this artifact must address?

**Evaluate against domain profile sections:** Regulatory & Compliance

**Look for:**
- Regulatory requirements that aren't addressed
- Data handling that violates industry compliance standards
- Missing audit trails or record-keeping requirements
- Privacy and consent requirements specific to this industry
- Licensing or certification requirements that affect design
- Cross-border or jurisdictional considerations

**Severity guide:**
- Critical: Clear regulatory violation (e.g., storing PCI data in plaintext)
- High: Missing compliance requirement that would block deployment
- Medium: Compliance gap that needs addressing but has workarounds
- Advisory: Proactive compliance improvement beyond minimum requirements

## Pass 4: Domain Pitfalls

**Question:** Does this artifact fall into known gotchas, edge cases, or failure modes specific to this industry?

**Evaluate against domain profile sections:** Anti-Patterns & Pitfalls

**Look for:**
- Known failure modes the artifact doesn't account for
- Edge cases that are common in this industry but rare elsewhere
- Assumptions that break under real-world industry conditions
- Historical mistakes others have made in this domain
- Scale or volume assumptions that don't match industry reality
- Temporal or seasonal patterns that affect the design

**Severity guide:**
- Critical: Known failure mode that will definitely occur (e.g., not handling fare rule complexity in travel)
- High: Common edge case that's unaddressed
- Medium: Known pitfall that's partially addressed
- Advisory: Rare but documented failure mode worth considering

## Pass 5: Integration Realities

**Question:** How does this interact with typical industry systems, standards, and third parties?

**Evaluate against domain profile sections:** Industry Systems & Integrations

**Look for:**
- Third-party system integrations that are missing or underspecified
- Data format mismatches with industry-standard systems
- API design that doesn't align with how industry partners expect to interact
- Missing error handling for common third-party failure modes
- Assumptions about data availability or latency from external systems
- Version compatibility with industry standard protocols

**Severity guide:**
- Critical: Integration approach that won't work with required industry systems
- High: Missing integration with a key industry partner/system
- Medium: Integration that works but doesn't follow industry conventions
- Advisory: Opportunity to use a more standard integration approach

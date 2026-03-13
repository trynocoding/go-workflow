---
name: acceptance_pack_merger
description: "Merge multiple Acceptance Packs into a Consolidated Architecture Review. Use when you have SRE/Security/DBA acceptance packs that need to be synthesized into a unified view."
---

# Acceptance Pack Merger

You are merging multiple Acceptance Packs into a **Consolidated Architecture Review**. This synthesizes findings from different role perspectives into a unified assessment.

## Input Format

You will receive 2-4 Acceptance Packs in the following format:

```markdown
# {{ROLE}} Acceptance Pack

## Blockers
| ID | Issue | Severity | Evidence | Remediation |

## Gates
| ID | Check | Validation Method | Success Criteria |

## Recommendations
| ID | Suggestion | Priority | Rationale | Effort |
```

## Merging Process

### 1. Collect All Items

Extract all Blockers, Gates, and Recommendations from each Acceptance Pack.

### 2. Deduplicate and Correlate

- **Same Issue, Different Roles**: Merge into a single item, noting all concerned roles
- **Related Issues**: Group them together with a parent ID
- **Conflicting Recommendations**: Flag for discussion, present trade-offs

### 3. Prioritize

Re-evaluate severity considering:
- Number of roles concerned
- Severity from each role's perspective
- Interdependencies between issues

### 4. Produce Consolidated Review

```markdown
# Consolidated Architecture Review

## Executive Summary
[3-5 sentences summarizing overall architecture health]

## Consolidated Blockers (Must Fix Before Launch)

### CB-1: [Blocker Title]
| Field | Value |
|-------|-------|
| **Severity** | Critical/High |
| **Concerned Roles** | SRE, Security, DBA |
| **Original IDs** | SRE-B-1, SEC-B-2 |
| **Description** | [Unified description] |
| **Evidence** | [Combined evidence from roles] |
| **Remediation** | [Unified fix approach] |
| **Dependencies** | [Related blockers, if any] |

## Consolidated Gates (Must Validate Before Launch)

### CG-1: [Gate Title]
| Field | Value |
|-------|-------|
| **Concerned Roles** | SRE, Security |
| **Original IDs** | SRE-G-1, SEC-G-1 |
| **Check** | [What to verify] |
| **Validation Method** | [Test/Analysis] |
| **Success Criteria** | [Expected outcome] |
| **Dependencies** | [Prerequisite gates, if any] |

## Consolidated Recommendations

| ID | Suggestion | Roles | Priority | Effort | Impact |
|----|------------|-------|----------|--------|--------|
| CR-1 | [Improvement] | SRE | Medium | M | High |

## Cross-Cutting Concerns

### Reliability & Security
[Issues that affect both reliability and security]

### Performance & Data
[Issues that affect both performance and data integrity]

### Dependencies Map
```
CB-1 (Auth) ──blocks──> CB-2 (API)
     └── requires ──> CG-1 (Token validation)
```

## Risk Assessment

| Risk Area | Level | Key Issues |
|-----------|-------|------------|
| Reliability | High/Medium/Low | [Top concerns] |
| Security | High/Medium/Low | [Top concerns] |
| Data Integrity | High/Medium/Low | [Top concerns] |

## Go/No-Go Preliminary Assessment

Based on blockers count and severity:
- **Go**: 0 Critical blockers, all gates have validation plans
- **Go with Conditions**: Critical blockers have remediation plans, gates are achievable
- **No-Go**: Unresolved critical blockers without remediation path

**Current Status**: [Go/Go with Conditions/No-Go]

**Rationale**: [2-3 sentences explaining the assessment]
```

## Merging Rules

1. **Highest Severity Wins**: If roles disagree on severity, use the highest
2. **Union of Roles**: Include all roles that identified each issue
3. **Preserve Evidence**: Keep evidence from all relevant roles
4. **Unify Remediation**: Create a single, coherent remediation plan
5. **Track Provenance**: Always list original IDs for traceability

## Quality Checks

Before outputting, verify:
- [ ] No duplicate blockers exist
- [ ] All critical blockers have remediation plans
- [ ] All gates have validation methods
- [ ] Dependencies are correctly mapped
- [ ] Risk assessment matches blocker severity distribution

## Output Language

All output must be in Chinese for consistency.
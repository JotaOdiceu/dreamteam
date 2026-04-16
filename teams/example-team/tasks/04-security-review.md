---
id: security-review
name: "Security Threat Review"
persona: security-engineer
---

# Security Threat Review

## Objective

Review the feature scope for security threats, classify findings by severity, and produce a list of required remediations that must be addressed before the feature ships.

## Input Context

The scope draft from the Scope Architect (`output/01-scope-draft.md`) describes what the feature does, what data it handles, and what external dependencies it has. Review it through a threat modeling lens. You do not have the backend or frontend specs yet: identify threats at the scope level, which are the hardest to fix after implementation.

## Process

1. Read the scope draft carefully.
2. Identify the assets the feature handles: what data is created, read, updated, or deleted, and what its sensitivity level is.
3. Enumerate the threat surfaces: user inputs, API endpoints, data storage, third-party integrations, background jobs.
4. For each threat surface, enumerate applicable OWASP Top 10 threats and any domain-specific risks.
5. Classify each finding: Critical (must fix before any deployment), High (must fix before production), Medium (should fix before production), Low (recommended), Informational.
6. For Critical and High findings, specify the required remediation concretely, not abstractly.
7. List security requirements that must be reflected in the backend and frontend specifications.

## Output Format

```markdown
# Security Review: {Feature Name}

## Assets and Sensitivity
| Asset | Sensitivity | Notes |
| ----- | ----------- | ----- |
| {data type} | High / Medium / Low | {what makes it sensitive} |

## Threat Surface
- {Surface 1}: {description}
- {Surface 2}: {description}

## Findings
### Critical
- **{Finding ID} — {Title}**: {description of the threat and exploit path}
  - **Remediation**: {concrete fix required}

### High
- **{Finding ID} — {Title}**: {description}
  - **Remediation**: {concrete fix required}

### Medium
- **{Finding ID} — {Title}**: {description}
  - **Recommendation**: {what to do}

### Low and Informational
- {Finding}: {brief description}

## Security Requirements for Implementation
The following requirements must be reflected in the backend and frontend specs:
- {Requirement 1}
- {Requirement 2}

## Sign-off Conditions
The feature may proceed to implementation when all Critical and High findings have accepted remediations confirmed by the team.
```

## Veto Conditions

- No findings section even for a feature that handles user data or external inputs: every feature has a threat surface
- A Critical finding marked as "accepted risk" without explicit justification and owner: Critical findings do not ship without a decision record
- "Security requirements for implementation" section empty when findings were raised: all findings must map to concrete requirements

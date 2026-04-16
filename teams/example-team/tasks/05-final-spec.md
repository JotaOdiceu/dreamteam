---
id: final-spec
name: "Final Technical Specification"
persona: scope-architect
---

# Final Technical Specification

## Objective

Consolidate the scope draft, backend spec, frontend spec, and security review into a single, coherent technical specification document that serves as the implementation contract for the team.

## Input Context

By this step, the following documents exist in the output folder:

- `output/01-scope-draft.md`: feature scope and user stories
- `output/02-backend-spec.md`: API contract, database schema, NestJS structure
- `output/03-frontend-spec.md`: routes, components, data fetching, forms
- `output/04-security-review.md`: threat findings and security requirements

Read all four documents. Identify conflicts, gaps, and inconsistencies between them (for example: a field in the frontend form that is not in the backend DTO, or a security requirement not reflected in either spec). Resolve or flag every conflict explicitly.

## Process

1. Read all four input documents.
2. Cross-reference them: find conflicts, gaps, and missing connections.
3. List every conflict found and how it is resolved (or escalated to the user if it requires a decision).
4. Consolidate all content into the final spec, structured for developer consumption.
5. Produce the Definition of Done: a checklist that, when fully checked, means the feature is complete and ready for production.

## Output Format

```markdown
# Technical Specification: {Feature Name}

**Version**: 1.0  
**Status**: Ready for Implementation  
**Team**: Feature Scoping Team

---

## Overview
{One paragraph: what the feature is, why it exists, who benefits.}

## Scope
### In Scope
- {item}

### Out of Scope
- {item}

---

## User Stories and Acceptance Criteria
| Story                           | Acceptance Criteria      |
|---------------------------------|--------------------------|
| As a {role}, I want to {action} | Given... When... Then... |

---

## Backend Specification

### Module Structure
{From backend spec, consolidated.}

### API Contract
| Method | Path | Auth | Request Body | Response | Status Codes |
|--------|------|------|--------------|----------|--------------|
| ...    | ...  | ...  | ...          | ...      | ...          |

### Database Schema
| Table | Column | Type | Constraints | Index |
|-------|--------|------|-------------|-------|

---

## Frontend Specification

### Routes
| Route | Rendering | Layout |
|-------|-----------|--------|

### Component Tree
{From frontend spec, consolidated.}

### Data Fetching and State
{From frontend spec, consolidated.}

---

## Security Requirements
{Security requirements from security review, mandatory for implementation.}

| Requirement | Severity | Applied In                |
|-------------|----------|---------------------------|
| {req}       | High     | Backend / Frontend / Both |

---

## Conflicts and Resolutions
| Conflict      | Resolution                         |
|---------------|------------------------------------|
| {description} | {how it was resolved or escalated} |

---

## Open Questions
| Question   | Owner          | Due                 |
|------------|----------------|---------------------|
| {question} | {name or role} | {date or milestone} |

---

## Definition of Done
- [ ] All API endpoints implemented and covered by e2e tests
- [ ] All DTOs validated with class-validator
- [ ] Database migration applied and tested on a clean database
- [ ] All frontend routes render correctly with loading, empty, and error states
- [ ] All Critical and High security findings remediated and verified
- [ ] OpenAPI documentation generated and reviewed
- [ ] Feature flagged if applicable, and flag removal planned
- [ ] PR reviewed and approved by at least one engineer from each area (backend, frontend, security)
```

## Veto Conditions

- "Conflicts and Resolutions" section empty when cross-referencing revealed mismatches: every conflict must be addressed
- Definition of Done missing any of the standard checklist items: the checklist is the minimum bar
- Open questions with no owner or due date: every open question must be assigned

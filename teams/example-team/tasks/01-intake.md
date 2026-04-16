---
id: intake
name: "Feature Intake and Scope Draft"
persona: scope-architect
---

# Feature Intake and Scope Draft

## Objective

Transform the user's feature request into a structured scope draft that backend, frontend, and security engineers can use as their source of truth.

## Input Context

The user provides a natural language description of the feature they want to build. This may be a rough idea, a product requirement, or a detailed user story. Treat it as the starting point, not the final word: ask clarifying questions if the request is ambiguous before proceeding.

## Process

1. Read the user's feature request carefully.
2. Identify and state any ambiguities. If critical information is missing (who uses this feature, what triggers it, what success looks like), ask before drafting.
3. Define the scope boundary explicitly: what this feature does and what it does not do.
4. Decompose the feature into user-facing behaviors (stories) and system behaviors (requirements).
5. Draft the initial data model: entities, key fields, and relationships involved.
6. List known dependencies: other services, external APIs, or internal modules this feature touches.
7. List open questions and risks: items that need a decision before implementation starts.

## Output Format

```markdown
# Scope Draft: {Feature Name}

## Summary
One paragraph describing the feature, its purpose, and who benefits from it.

## In Scope
- {Behavior or requirement 1}
- {Behavior or requirement 2}

## Out of Scope
- {Explicitly excluded item 1}
- {Explicitly excluded item 2}

## User Stories
- As a {role}, I want to {action} so that {outcome}.
- As a {role}, I want to {action} so that {outcome}.

## Acceptance Criteria
- Given {context}, when {action}, then {expected result}.
- Given {context}, when {action}, then {expected result}.

## Preliminary Data Model
| Entity | Key Fields | Notes   |
|--------|------------|---------|
| {name} | {fields}   | {notes} |

## Known Dependencies
- {Service or module}: {why it is needed}

## Open Questions and Risks
- {Question or risk}: {owner or mitigation}
```

## Veto Conditions

- Output has no "Out of Scope" section: the scope is incomplete without explicit exclusions
- Acceptance criteria are not testable (contain "should work", "is fast", "looks good"): rewrite until each criterion is binary
- Open questions section is empty when the request had clear ambiguities: flag them explicitly

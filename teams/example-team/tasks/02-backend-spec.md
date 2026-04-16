---
id: backend-spec
name: "Backend API and Data Model Specification"
persona: backend-engineer
---

# Backend API and Data Model Specification

## Objective

Produce the complete backend specification for the feature: the NestJS module structure, the full API contract, the database schema, and any authentication or authorization requirements.

## Input Context

The scope draft from the Scope Architect (`output/01-scope-draft.md`) defines what the feature does and its data model. Use it as the source of truth and expand it into an implementable backend specification.

## Process

1. Read the scope draft carefully.
2. Define the NestJS module structure: which modules are created or modified, what each exports.
3. Design the full API contract: one table per endpoint with method, path, auth requirement, request body, and response shape.
4. Expand the preliminary data model into a full database schema with field types, constraints, indexes, and migration notes.
5. Specify authentication and authorization: which Guard applies to each endpoint, what roles or permissions are required.
6. List the DTOs needed: one per request body and one per response shape.
7. Identify any background jobs, queues, or event emissions the feature requires.
8. Flag any performance concerns: queries that may need pagination, caching, or denormalization.

## Output Format

```markdown
# Backend Specification: {Feature Name}

## NestJS Module Structure
- **{ModuleName}Module** (new/modified): {responsibility}
  - Imports: {list}
  - Providers: {list of services and repositories}
  - Exports: {list}

## API Contract
| Method | Path | Auth             | Request Body DTO | Response DTO | Status Codes       |
|--------|------|------------------|------------------|--------------|--------------------|
| POST   | /... | JWT (role: user) | CreateXDto       | XResponseDto | 201, 400, 401, 409 |

## Database Schema
| Table   | Column | Type   | Constraints      | Index  |
|---------|--------|--------|------------------|--------|
| {table} | {col}  | {type} | NOT NULL, UNIQUE | yes/no |

### Migration Notes
{Describe what the migration does and any data backfill needed.}

## DTOs
- **CreateXDto**: {fields and validations}
- **XResponseDto**: {fields exposed in the response}

## Auth and Authorization
- {Endpoint}: requires {Guard}, {Role/Permission}

## Background Jobs and Events
- {Job or event}: {trigger and payload}

## Performance Considerations
- {Query or operation}: {concern and mitigation}
```

## Veto Conditions

- Any endpoint missing its auth requirement specification: all endpoints must declare their auth posture
- Response DTOs not defined: never expose the entity directly
- Database schema missing field types or constraints: incomplete schema is not shippable

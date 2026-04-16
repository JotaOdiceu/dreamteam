---
id: frontend-spec
name: "Frontend Implementation Specification"
persona: frontend-engineer
---

# Frontend Implementation Specification

## Objective

Produce the complete frontend implementation plan for the feature: the Next.js route and component structure, data fetching strategy, state management approach, and UI behavior specification.

## Input Context

The scope draft from the Scope Architect (`output/01-scope-draft.md`) defines the user stories and acceptance criteria. Use them to derive the frontend implementation. Do not invent requirements: if something is not in the scope draft, flag it as an assumption.

## Process

1. Read the scope draft carefully, focusing on user stories and acceptance criteria.
2. Define the routes and layouts needed: new pages, nested layouts, or modifications to existing ones.
3. For each route, decide the rendering strategy (Server Component, Client Component, or mixed) and justify it.
4. Map out the component tree: which components are server-side, which are client-side, and why.
5. Specify the data fetching strategy: what data is fetched on the server, what is fetched client-side, and with which mechanism (fetch in RSC, React Query, SWR, Server Action).
6. Define state management needs: what local state exists, what requires a global store.
7. Specify form handling: which forms exist, their fields, validation rules (Zod schema), and submission flow.
8. List loading, empty, and error states for every data-dependent UI section.
9. Flag any third-party components or libraries needed.

## Output Format

```markdown
# Frontend Specification: {Feature Name}

## Routes and Layouts
| Route         | Type         | Rendering        | Layout        |
|---------------|--------------|------------------|---------------|
| /feature      | Page         | Server Component | RootLayout    |
| /feature/[id] | Dynamic Page | Mixed            | FeatureLayout |

## Component Tree
- `FeaturePage` (Server Component): fetches initial data, renders layout
  - `FeatureList` (Server Component): maps over items
    - `FeatureCard` (Server Component): displays a single item
  - `CreateFeatureButton` (Client Component, "use client"): opens modal
    - `CreateFeatureModal` (Client Component): form with optimistic update

## Data Fetching Strategy
| Data             | Where Fetched | Mechanism      | Notes                     |
|------------------|---------------|----------------|---------------------------|
| List of items    | Server        | fetch() in RSC | Cached with revalidateTag |
| User preferences | Client        | React Query    | Stale-while-revalidate    |

## State Management
- {State}: {location (local/Zustand/form)} — {reason}

## Forms
- **{FormName}**: fields ({list}), Zod schema ({key validations}), submission via {Server Action / API route}

## Loading, Empty, and Error States
| Section   | Loading            | Empty                | Error            |
|-----------|--------------------|----------------------|------------------|
| {section} | Skeleton component | "No items yet" + CTA | Toast with retry |

## Third-party Components and Libraries
- {Library}: {use case and justification}

## Assumptions
- {Assumption flagged as not in scope draft}
```

## Veto Conditions

- A "use client" directive added without documented justification: every client boundary is a deliberate decision
- Form missing its Zod validation schema specification: validation is non-negotiable
- Data fetching happening client-side when a Server Component could handle it: justify the choice or move it server-side
- Loading and error states omitted for any data-dependent section

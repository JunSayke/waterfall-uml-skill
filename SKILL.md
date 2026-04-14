---
name: waterfall-uml-skill
description: >
  Generate a full suite of UML diagrams as artifacts in a consistent, cross-referenced,
  chronological workflow. Use this skill whenever the user asks to model, design, document,
  or diagram a system, feature, module, or application — even if they don't say "UML"
  explicitly. Trigger phrases include: "create diagrams for", "model this system", "draw
  the flow", "design the architecture", "generate UML", "document this module", "show the
  use cases", "sequence diagram", "class diagram", "ERD", "activity diagram", "wireframe",
  or any combination thereof. Also trigger when the user provides a spec, PRD, or
  requirements doc and wants it turned into visual artifacts. Always use this skill when
  multiple diagram types are needed — do not attempt ad hoc multi-diagram generation without it.
---

# UML Diagrams — Artifacts-First Workflow

Generate a consistent, cross-referenced suite of UML artifacts for any system or module.
Diagrams are produced in **chronological workflow order**, each building on the previous,
so that naming, actors, flows, and entities remain coherent across the full set.

> **Examples**: See `examples/` folder for a complete reference set (User Authentication
> module). Each file demonstrates correct structure, naming, and scope for its diagram type.

---

## 0. Pre-flight

### Ask the user (once, before generating anything)

Ask this question in a single message:

**Output location** — Where should the artifact files be saved?

> Default (press Enter to accept):
>
> ```
> docs/<module-name>/diagrams/*.puml
> ```
>
> A custom path is accepted as-is.

---

### Collect context

| Item                   | Source                                |
| ---------------------- | ------------------------------------- |
| Module / feature name  | User prompt or spec                   |
| Actors / roles         | User prompt, PRD, or infer            |
| Tech stack             | Spec, prompt, or ask once             |
| Optional diagram scope | Infer from domain; confirm if unclear |
| Existing specs or docs | Cross-reference if provided           |

If a spec or PRD is provided, extract before diagramming:

- Actors, roles, user stories
- Key entities and relationships
- System boundaries and external integrations
- Technology constraints (language, framework, DB, patterns)

If the tech stack is known, confirm best practices before drawing:

- Correct pattern names (Repository, CQRS, MVC, etc.) for class/sequence diagrams
- DB type: relational → include ERD; document store → skip or adapt
- Naming conventions: PascalCase (Java/C#/Kotlin), snake_case (Python/Ruby)

> **Web search / Context7**: Use either tool when the stack is modern or unfamiliar to
> confirm current conventions before proceeding.

---

## 1. Diagram Workflow — Chronological Order

Produce diagrams in this sequence. Each feeds into the next.

```
① Use Case Diagram + Descriptions   (always)
② Activity Diagram                  (always)
③ Sequence Diagram                  (always)
④ ERD                               (optional — see §3.4)
⑤ Class Diagram                     (optional — OOP preferred; see §3.5)
⑥ Low-Fidelity Wireframe            (optional — SVG; see §3.6)
```

Each diagram is a **separate file** at the output path confirmed in §0.

File naming convention:

```
usecase.puml
activity.puml
sequence.puml              (or sequence-<uc-id>.puml if multiple)
erd.puml
class.puml
wireframe.svg              (or wireframe-<screen>.svg if multiple screens)
```

---

## 2. PlantUML Format — Strict Template

Every `.puml` file MUST use exactly this header — **no additional skinparam**:

```
@startuml
skinparam monochrome true

' ──────────────────────────────────────────
' Module : <Module Name>
' Diagram: <Diagram Type>
' ──────────────────────────────────────────

' ... diagram content ...

@enduml
```

`skinparam monochrome true` is the **only** skinparam permitted.
Do not add font, color, arrow, border, or background skinparam of any kind.

---

## 3. Diagram Instructions

### Scope rule (applies to all diagrams)

Diagrams express **behavior and structure** — not implementation.

Never include:

- SQL types, constraints, or indexes
- Method bodies or algorithmic steps
- Framework annotations (`@Autowired`, `@Injectable`, decorators)
- Internal data transformations within a single component
- Infrastructure details (ports, hostnames, environment variables)

If it belongs in code, it does not belong in a diagram.

---

### 3.1 Use Case Diagram

**Purpose**: Define actors, system boundary, and high-level capabilities.

**Rules**:

- Define ALL actors first — these names propagate verbatim to all other diagrams
- Wrap use cases in `rectangle "Module Name" { }` as the system boundary
- Number every use case: `UC-01`, `UC-02`, … — cite these IDs in ② and ③
- Use `<<include>>` for mandatory sub-flows, `<<extend>>` for optional variations
- Use verb phrases for use case names: "Log In", "Place Order", "Reset Password"

See `examples/usecase.puml` for a complete reference.

**After the diagram**, output a **Use Case Descriptions table** inline in conversation:

| UC-ID | Name | Actor(s) | Precondition | Main Flow | Postcondition | Exceptions |
| ----- | ---- | -------- | ------------ | --------- | ------------- | ---------- |

Main Flow: 2–4 plain-language steps. No code or implementation detail.

---

### 3.2 Activity Diagram

**Purpose**: Show the business workflow — decisions, branches, and parallel paths.

**Rules**:

- Use swimlanes `|Actor|` matching actors from 3.1
- Add a header comment citing relevant UC-IDs: `' Covers UC-01, UC-02`
- Happy path first, then branch for alternate/error flows using `if/else`
- Use `fork` / `fork again` only for genuinely parallel steps
- Every path ends at `stop` or a shared `end` node
- Activity labels are plain-language actions ("Verify credentials"), not code calls

See `examples/activity.puml` for a complete reference.

---

### 3.3 Sequence Diagram

**Purpose**: Show message exchange between participants for a specific scenario.

**Rules**:

- One sequence diagram per major use case; group minor ones if closely related
  - If > 5 use cases, cover the 3 most critical; note the others as deferred
- Header comment cites the UC-ID: `' UC-02 Log In`
- Participant names match actors/components from 3.1 and 3.2
- If tech stack is known, use real layer names (Controller, Service, Repository)
  but stay at the interface level — no internal logic inside a participant
- Use `alt` for branching, `opt` for optional steps, `loop` for repetition
- Message labels: operation name + key parameter names only ("login(email, password)")

See `examples/sequence.puml` for a complete reference.

---

### 3.4 ERD _(optional)_

**Include when**:

- A relational database is in scope (PostgreSQL, MySQL, SQLite, etc.)
- The domain has ≥ 3 entities with meaningful relationships
- User explicitly requests it

**Skip when**: NoSQL / document store only, or no persistent schema in scope.

**Rules**:

- Crow's foot notation: `Entity1 ||--o{ Entity2 : "verb label"`
- Entity names match domain objects from 3.1–3.3
- Show only: PK, FK, and 3–5 key business attributes per entity
- No data types, constraints, indexes, or migration history
- Every relationship line has cardinality and a verb label

See `examples/erd.puml` for a complete reference.

---

### 3.5 Class Diagram _(optional — OOP preferred)_

**Include when**:

- OOP tech stack is in use (Java, C#, Kotlin, TypeScript, Python, Swift, etc.)
- System has meaningful domain logic beyond CRUD
- User requests it

**Skip when**: Pure functional, scripting-only, or no domain model warranted.

**Rules**:

- Class names match entity/component names from 3.1–3.4
- Show visibility: `+` public, `-` private, `#` protected
- List key methods with parameter names — no return types unless intent is unclear
- Apply design patterns using `<<stereotype>>` labels (`<<Service>>`, `<<Repository>>`)
- Relationship arrows: `implements`, `extends`, `-->` dependency, `*--` composition, `o--` aggregation
- No constructors, getters/setters, or framework annotations

See `examples/class.puml` for a complete reference.

---

### 3.6 Low-Fidelity Wireframe _(optional)_

**Format**: SVG file — `wireframe.svg` or `wireframe-<screen>.svg`

**Include when**:

- System has a user-facing interface
- User requests a wireframe, mockup, or screen sketch
- Spec describes screen layouts or user journeys

**Rules**:

- Grayscale only — fills: `#fff`, `#f5f5f5`, `#e0e0e0`; strokes: `#333`
- Basic shapes and plain-text labels only: `[ Button ]`, `[ Input ]`, `[ Dropdown ]`
- Annotate each screen with its UC-ID
- One SVG per distinct screen

```xml
<svg xmlns="http://www.w3.org/2000/svg" width="800" height="600"
     font-family="Arial" font-size="13" fill="#333">
  <!-- Screen: <Name> | UC-XX -->
  <rect width="800" height="600" fill="#fff" stroke="#333" stroke-width="1"/>
</svg>
```

---

## 4. Cross-Reference & Consistency Rules

| Rule                   | Requirement                                                               |
| ---------------------- | ------------------------------------------------------------------------- |
| **Actor names**        | Defined in 3.1 — used verbatim in 3.2, 3.3, 3.5, 3.6                      |
| **Entity names**       | Introduced in 3.4 — must match class names in 3.5 and participants in 3.3 |
| **UC-IDs**             | Assigned in 3.1 — cited in header comments of 3.2 and 3.3                 |
| **Naming conventions** | Follow the target tech stack consistently throughout                      |
| **Layer alignment**    | Participant names in 3.3 must match architectural layers                  |
| **Pattern labels**     | Stereotypes in 3.5 must align with component roles in 3.3                 |
| **No orphan elements** | Every actor or entity in one diagram must appear in at least one other    |

---

## 5. Output Checklist

Before presenting the final set:

- [ ] Output path confirmed with user before generating
- [ ] Every `.puml` uses only `skinparam monochrome true`
- [ ] Actor names are identical across all diagrams
- [ ] UC-IDs are defined in 3.1 and cited in 3.2 and 3.3
- [ ] Diagrams are at behavioral/structural level — no implementation detail
- [ ] Optional diagrams: included with brief rationale or skipped with reason stated
- [ ] README.md generated and saved to `docs/<module-name>/README.md`
- [ ] File names match the convention in §1

---

## 6. README.md — Spec Document

The skill always generates a spec document and saves it to `docs/<module-name>/README.md`.

**Strictly follow this format — do not add, remove, or reorder sections.**

```markdown
# {Module Name}

> **Status**: Draft | Review | Approved
> **Last Updated**: YYYY-MM-DD
> **Owner**: {Team or Author}

---

## Overview

One paragraph. What is this module? What problem does it solve? Who uses it?

---

## Actors

| Actor   | Role           |
| ------- | -------------- |
| {Actor} | {One sentence} |

---

## Use Cases

| UC-ID | Name   | Primary Actor | Short Description |
| ----- | ------ | ------------- | ----------------- |
| UC-01 | {Name} | {Actor}       | {One sentence}    |

---

## Use Case Descriptions

Provide a table with the detailed description for each critical use case. This captures the precondition, main flow (2–6 steps), postcondition, and exceptions.

| UC-ID | Name   | Actor(s) | Precondition   | Main Flow            | Postcondition   | Exceptions   |
| ----- | ------ | -------- | -------------- | -------------------- | --------------- | ------------ |
| UC-01 | {Name} | {Actor}  | {Precondition} | 1. {Step}\n2. {Step} | {Postcondition} | {Exceptions} |

---

## Flows

One subsection per critical use case for readability. Keep to the "what", not the "how".

### UC-XX: {Name}

1. {Step}
2. {Step}

---

## Domain Entities

| Entity   | Responsibility |
| -------- | -------------- |
| {Entity} | {One sentence} |

---

## Constraints & Rules

- {Business rule — state the rule, not the mechanism}

---

## Out of Scope

- {What this module does not cover}

---

## Diagrams

| Diagram  | File                     |
| -------- | ------------------------ |
| Use Case | `diagrams/usecase.puml`  |
| Activity | `diagrams/activity.puml` |
| Sequence | `diagrams/sequence.puml` |
| ERD      | `diagrams/erd.puml`      |
| Class    | `diagrams/class.puml`    |
```

**README rules**:

- Flows: 3–8 numbered steps per use case; each step is one plain-language action
- Constraints: state the rule, not the implementation ("Passwords are never stored in plain text"
  — not "BCrypt cost factor 12")
- No code snippets, API endpoints, or field names in prose sections
- Must be understandable by a non-technical stakeholder

See `examples/README.template.md` for a filled-in reference example.

---

## 7. Communication

- Ask the two pre-flight questions (§0) once, before generating anything
- State which diagrams will be produced and why (1–2 sentences)
- After the full set, write a short **Consistency Summary** (≤ 100 words) noting
  how diagrams cross-reference each other and flagging any spec ambiguities found
- If scope is ambiguous, ask one clarifying question — then proceed with reasonable defaults

# UML Diagrams Agent Skill

An AI agent skill that equips your coding assistant (VScode, Claude Code, Cursor, Opencode, etc.) with a strict, artifacts-first workflow for generating consistent, cross-referenced UML diagrams.

Instead of getting ad-hoc, disjointed diagrams, this skill forces the LLM to generate a complete chronological suite (Use Case → Activity → Sequence → ERD → Class → Wireframes) where actors, entities, and use-case IDs remain perfectly consistent across the entire system model.

## Installation

You can install this skill directly into your agent's context using the Open Agent Skills CLI:

```bash
npx skills add junsayke/waterfall-uml-skill
```

## How It Works

Once installed, the skill runs automatically when you ask your AI agent to design, model, or document a system. It follows a strict pre-flight checklist and chronological workflow.

**Trigger Phrases:**
You can trigger the skill by providing a PRD/Spec or simply asking the agent:

- _"Create diagrams for the new authentication module"_
- _"Model this system"_
- _"Generate UML for the checkout flow"_
- _"Design the architecture for my Next.js app"_

### The Workflow Pipeline

The agent is instructed to build diagrams in this exact order, ensuring each feeds context into the next:

1. **Use Case Diagram** (Identifies Actors & UC-IDs)
2. **Activity Diagram** (Business logic flows)
3. **Sequence Diagram** (System message exchange)
4. **ERD** _(Optional - for relational data)_
5. **Class Diagram** _(Optional - for OOP stacks)_
6. **Low-Fidelity Wireframes** _(Optional - SVG format)_

## Repository Structure

```text
.
├── SKILL.md                   # Core system prompt and workflow instructions
└── examples/                  # Reference examples injected into the LLM context
    ├── activity.puml          # Reference Activity diagram
    ├── class.puml             # Reference Class diagram
    ├── erd.puml               # Reference ERD diagram
    ├── sequence.puml          # Reference Sequence diagram
    ├── usecase.puml           # Reference Use Case diagram
    └── README.template.md     # Template for generating system spec documentation (use case description is define here)
```

## Features

- **Zero-Hallucination Formatting:** Enforces strict `@startuml` formatting with `skinparam monochrome true` and blocks the LLM from adding unsupported styling.
- **Intelligent Output Routing:** Automatically prompts the user for a save location (defaults to `docs/<module-name>/diagrams/`).
- **Technology Aware:** Automatically tailors Class Diagrams and ERDs to the user's specific tech stack.

## License

MIT

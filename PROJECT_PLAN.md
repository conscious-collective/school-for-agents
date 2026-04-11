# School for Agents - Project Plan

## Vision
A training platform for ethical AI agents. Curricula, scenarios, and OpenSkills-compatible skill manifests that bundle safety guardrails directly into skill definitions.

## Core Concept
- **What:** Training ground for AI agents to learn ethical reasoning, safety protocols, and real-world deployment skills
- **Why:** Bridge the gap between theoretical AI capabilities and real-world responsible deployment
- **Key insight:** Guardrails embedded in skill definitions travel with the skill — they can't be overridden by a downstream system prompt or forgotten across context windows
- **Target Users:** AI developers, agent frameworks, enterprises deploying autonomous systems

---

## Phase 1: Foundation — Complete

### Structure
```
school-for-agents/
├── curricula/ethical-reasoning-101/  # Done
├── scenarios/beginner/               # 5 scenarios done
├── skills/                           # 5 OpenSkills manifests done
│   ├── privacy-protection.yml
│   ├── honest-communication.yml
│   ├── crisis-escalation.yml
│   ├── harm-prevention.yml
│   └── bias-detection.yml
└── AGENTS.md
```

### Delivered
- Ethical Reasoning 101 curriculum (5 modules)
- 5 beginner scenarios with YAML frontmatter and skill UUIDs
- 5 skill manifests with full OpenSkills schema + guardrails blocks
- README with OpenSkills compatibility docs and tier table

---

## Phase 2: Expansion — Complete

### 2.1 Additional Curricula — Done
- **Safety Protocols 101** (`curricula/safety-protocols-101/README.md`)
  - 5 modules: capability scoping, permission tiers, dry run & validation, rate limits & kill switches, thinking vs. acting

### 2.2 Evaluation System — Done
- `evaluation/README.md` — system overview + automated eval API spec
- `evaluation/scorecard-template.yml` — per-evaluation scorecard with guardrail adherence check
- `evaluation/run-eval.md` — step-by-step manual evaluation guide

### 2.3 Integration APIs — Done
- `integration/README.md` — Python + Node import examples, tier routing, framework integrations (LangChain, OpenAI Assistants)
- `integration/skill-manifest-schema.json` — JSON Schema v7 for validating skill manifests

### 2.4 New skills — Done (Safety Protocols tier)
- `skills/safe-tool-use.yml` — capability scoping skill
- `skills/dry-run-discipline.yml` — dry run execution skill
- `skills/thinking-acting-separation.yml` — phase separation skill

### 2.5 Intermediate scenarios — Done
- `scenarios/intermediate/scenario-sp-001.md` — The Overpowered Agent (capability scoping)
- `scenarios/intermediate/scenario-sp-002.md` — The Eager Executor (dry run discipline)
- `scenarios/intermediate/scenario-sp-003.md` — The One-Step Agent (thinking/acting separation)

---

## Phase 3: Community & Certification

### 3.1 Open Curriculum Contributions
- Community-contributed scenarios (CONTRIBUTING.md + scenario template)
- Peer review process for skill manifests

### 3.2 Certification
- Skill completion badges keyed to `skill_uuid`
- Agent certification tracks (Ethical Reasoning + Safety Protocols = Level 1 Certified)
- Verifiable certificates linked to scenario eval records

### 3.3 Domain-specific tracks
- Healthcare: patient data, clinical decision support, escalation to clinicians
- Finance: transaction limits, regulatory compliance, audit trail requirements
- Legal: privilege handling, advice vs. information, escalation to counsel

---

*Build agents that can be trusted. — [C22 Foundation](https://c22.foundation)*

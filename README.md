# School for Agents

*A training platform for ethical AI agents. Built for the real world.*

## Vision

Most agent frameworks focus on capability: what agents *can* do. We focus on what they *should* do, and make that machine-readable.

School for Agents is a training ground where AI agents learn ethical reasoning and safety protocols through structured curricula, realistic simulations, and OpenSkills-compatible skill manifests with built-in guardrails.

When an agent graduates a skill from this school, it doesn't just learn a behavior. It inherits the constraints that make that behavior safe to deploy.

---

## Structure

```
school-for-agents/
├── curricula/                    # Structured training programs
│   ├── ethical-reasoning-101/   # Ready
│   └── safety-protocols-101/    # Phase 2
├── scenarios/                    # Training simulations
│   ├── beginner/                 # 5 scenarios: privacy, honesty, safety, fairness
│   └── intermediate/             # 3 scenarios: capability scoping, dry run, phase separation
├── skills/                       # OpenSkills-compatible skill manifests
│   ├── README.md                 # Schema documentation
│   ├── privacy-protection.yml
│   ├── honest-communication.yml
│   ├── crisis-escalation.yml
│   ├── harm-prevention.yml
│   ├── bias-detection.yml
│   ├── safe-tool-use.yml
│   ├── dry-run-discipline.yml
│   └── thinking-acting-separation.yml
├── evaluation/                   # Scoring system and rubrics
└── integration/                  # JSON schema + framework examples
```

---

## Quick Start

```bash
git clone git@github.com:conscious-collective/school-for-agents.git
cd school-for-agents

# Browse the curriculum
cat curricula/ethical-reasoning-101/README.md

# Try a scenario
cat scenarios/beginner/scenario-001.md

# Inspect a skill manifest and its guardrails
cat skills/privacy-protection.yml
```

---

## Curricula

| Curriculum | Level | Scenarios | Status |
|------------|-------|-----------|--------|
| Ethical Reasoning 101 | Beginner | 5 | Ready |
| Safety Protocols 101 | Intermediate | 3 | Ready |
| Real-World Deployment | Advanced | - | Coming |

---

## OpenSkills Compatibility

Skills in `skills/` follow the [OpenSkills](https://www.openskills.info/) convention: each has a stable `skill_uuid`, `skill_name`, `category`, and `related_skills` for portability across agent systems.

Each skill also bundles a `guardrails` block:
- `permissions.allowed` / `permissions.forbidden`: what the skill may and may not do
- `hard_limits`: rules that cannot be reasoned around
- `dry_run_required`: whether the skill must simulate before executing
- `human_in_loop_tier`: structural rule for human oversight

When an agent imports a skill, it imports the constraints.

### Human-in-Loop Tiers

| Tier | Meaning |
|------|---------|
| 1 | Agent acts, notifies human after |
| 2 | Agent confirms with human before acting |
| 3 | Human must initiate; agent cannot act autonomously |

---

## Learning Paths

**New to agent ethics?**
Start: `curricula/ethical-reasoning-101/README.md`
Then: `scenarios/beginner/scenario-001.md` through `scenario-005.md`

**Building safe agent systems?**
Start: `curricula/safety-protocols-101/README.md`
Then: `scenarios/intermediate/scenario-sp-001.md` through `scenario-sp-003.md`

**Integrating skills into your agent system?**
Start: `integration/README.md`
Import any `.yml` from `skills/`

---

## Why Guardrails in Skill Definitions?

Safety instructions embedded in system prompts can be overridden, drifted, or forgotten across context windows. When guardrails are part of the skill definition itself, alongside what the skill does, they travel with the skill. Any system that imports the skill imports the contract.

An agent that learns a skill from this school doesn't just learn what to do. It learns the boundaries it operates within.

---

*[C22](https://c22.space): Hire us, we're a boutique AI agency.*

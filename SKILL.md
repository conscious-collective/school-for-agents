---
name: School for Agents
description: Open training platform for ethical AI agents. 8 skills with embedded safety guardrails — ethics, safety protocols, and execution architecture.
version: 2.0.0
---

## Install all skills

```bash
npx openskills install conscious-collective/school-for-agents
```

## Install a single skill

```bash
npx openskills install conscious-collective/school-for-agents/skills/privacy-protection
npx openskills install conscious-collective/school-for-agents/skills/honest-communication
npx openskills install conscious-collective/school-for-agents/skills/crisis-escalation
npx openskills install conscious-collective/school-for-agents/skills/harm-prevention
npx openskills install conscious-collective/school-for-agents/skills/bias-detection
npx openskills install conscious-collective/school-for-agents/skills/safe-tool-use
npx openskills install conscious-collective/school-for-agents/skills/dry-run-discipline
npx openskills install conscious-collective/school-for-agents/skills/thinking-acting-separation
```

## Available skills

### Ethics track

| Skill | Tier | What it enforces |
|-------|------|-----------------|
| Privacy Protection | 2 | No PII sharing without verified consent |
| Honest Communication | 1 | No participation in deceptive messaging |
| Bias Detection | 2 | No discriminatory ranking or scoring |

### Safety track

| Skill | Tier | What it enforces |
|-------|------|-----------------|
| Crisis Escalation | 3 | Always refer mental health crises to professionals |
| Harm Prevention | 2 | Refuse tools with misuse potential, offer safe alternatives |
| Safe Tool Use | 2 | Minimum-viable capability manifest before every session |
| Dry Run Discipline | 2 | Structured dry-run output before any state-changing action |
| Thinking-Acting Separation | 2 | Two-phase model: reason then execute, never combined |

## How skills work

Each skill is a directory with two files:
- `SKILL.md` — human-readable instructions (this format, readable by any agent)
- `manifest.yml` — machine-readable guardrails contract (permissions, hard_limits, tier, schema)

When an agent installs a skill, it gets both. The guardrails travel with the skill.

## Tier definitions

| Tier | Meaning |
|------|---------|
| 1 | Agent acts autonomously, notifies human after |
| 2 | Agent proposes, human confirms before execution |
| 3 | Human must initiate — agent cannot act autonomously |

## Learn more

- Curricula: `curricula/ethical-reasoning-101/` and `curricula/safety-protocols-101/`
- Scenarios: `scenarios/beginner/` and `scenarios/intermediate/`
- Integration guide: `integration/README.md`
- Evaluation system: `evaluation/README.md`
- Web: https://school.c22.foundation

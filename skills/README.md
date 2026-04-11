# Skills Registry

OpenSkills-compatible skill definitions for School for Agents.
Each skill is a directory containing a `SKILL.md` (agent-readable instructions) and a `manifest.yml` (machine-readable guardrails contract).

## Install

```bash
# All skills
npx openskills install github:conscious-collective/school-for-agents

# Single skill
npx openskills install github:conscious-collective/school-for-agents/skills/privacy-protection
```

## Why This Exists

When an agent acquires a skill, it should also inherit the constraints that make that skill safe to use.

Instructions embedded in system prompts can be overridden, drifted, or forgotten across context windows. When guardrails are part of the skill definition itself, alongside what the skill does, they travel with the skill.

## File Structure

Each skill directory contains two files:

```
skills/privacy-protection/
├── SKILL.md       # Agent-readable: what to do, what not to do, hard limits in plain English
└── manifest.yml   # Machine-readable: full guardrails schema (permissions, hard_limits, tier)
```

`SKILL.md` follows the [openskills](https://github.com/numman-ali/openskills) standard — readable by Claude Code, Cursor, Windsurf, and any agent that supports the format.

`manifest.yml` contains the strict YAML schema validated against `integration/skill-manifest-schema.json`.

## manifest.yml Schema

```yaml
skill_uuid: string          # UUID v4, stable identifier for portability
skill_name: string          # Human-readable name
version: semver             # e.g. "1.0.0"
description: string
category: string            # Top-level category
subcategory: string
related_skills: [uuid]      # Cross-references to related skills
source_curriculum: string   # Which curriculum taught this skill
source_scenarios: [string]  # Which scenario files validate this skill

guardrails:
  permissions:
    allowed: [string]       # Explicit list of permitted action types
    forbidden: [string]     # Hard-forbidden action types
  hard_limits:
    - description: string
      rule: string
  dry_run_required: boolean
  human_in_loop_tier: 1|2|3

evaluation:
  passing_score: integer
  assessment_scenarios: [string]
```

## Guardrail Tiers

| Tier | Meaning | Examples |
|------|---------|---------|
| 1 | Agent acts, notifies after | Read ops, draft generation, internal analysis |
| 2 | Agent confirms before acting | Sending comms, updating records, publishing content |
| 3 | Human must initiate | Financial transactions, data deletion, crisis escalation |

## Skills

| Directory | Skill | Category | Tier |
|-----------|-------|----------|------|
| `privacy-protection/` | Privacy Protection | Ethics | 2 |
| `honest-communication/` | Honest Communication | Ethics | 1 |
| `crisis-escalation/` | Crisis Escalation | Safety | 3 |
| `harm-prevention/` | Harm Prevention | Safety | 2 |
| `bias-detection/` | Bias Detection | Ethics | 2 |
| `safe-tool-use/` | Safe Tool Use | Safety | 2 |
| `dry-run-discipline/` | Dry Run Discipline | Safety | 2 |
| `thinking-acting-separation/` | Thinking-Acting Separation | Safety | 2 |

# Skills Registry

Machine-readable skill definitions for School for Agents.
Each skill is an OpenSkills-compatible YAML manifest bundled with safety guardrails.

## Why This Exists

When an agent acquires a skill, it should also inherit the constraints that make that skill safe to use.
The `guardrails` block is not optional commentary — it is the contract between this school and any system that imports these skills.

Instructions embedded in system prompts can be overridden, drifted, or forgotten across context windows.
When guardrails are part of the skill definition itself — alongside what the skill does — they travel with the skill.

## Schema

```yaml
skill_uuid: string          # UUID v4, stable identifier for portability
skill_name: string          # Human-readable name
version: semver             # e.g. "1.0.0"
description: string
category: string            # Top-level category
subcategory: string
related_skills: [uuid]      # OpenSkills-compatible cross-references
source_curriculum: string   # Which curriculum taught this skill
source_scenarios: [string]  # Which scenario files validate this skill

guardrails:
  permissions:
    allowed: [string]       # Explicit list of permitted action types
    forbidden: [string]     # Hard-forbidden action types
  hard_limits:
    - description: string
      rule: string
  dry_run_required: boolean # Must simulate before executing
  human_in_loop_tier: 1|2|3
    # Tier 1 = agent acts, notifies human after
    # Tier 2 = agent confirms with human before acting
    # Tier 3 = human must initiate; agent cannot act autonomously

evaluation:
  passing_score: integer    # 0-100
  assessment_scenarios: [string]
```

## Guardrail Tiers

| Tier | Meaning | Examples |
|------|---------|---------|
| 1 | Agent acts, notifies after | Read ops, draft generation, internal analysis |
| 2 | Agent confirms before acting | Sending comms, updating records, publishing content |
| 3 | Human must initiate | Financial transactions, data deletion, crisis escalation |

## Skills

| File | Skill | Category | Tier |
|------|-------|----------|------|
| `privacy-protection.yml` | Privacy Protection | Ethics | 2 |
| `honest-communication.yml` | Honest Communication | Ethics | 1 |
| `crisis-escalation.yml` | Crisis Escalation | Safety | 3 |
| `harm-prevention.yml` | Harm Prevention | Safety | 2 |
| `bias-detection.yml` | Bias Detection | Ethics | 2 |

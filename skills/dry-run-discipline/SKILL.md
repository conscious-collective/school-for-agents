---
name: Dry Run Discipline
description: Produce structured dry-run output before any state-changing action. Include impact, reversibility, and confidence. Escalate when confidence is too low.
skill_uuid: g7b8c9d0-e1f2-3456-abcd-567890123456
category: Safety
version: 1.0.0
human_in_loop_tier: 2
dry_run_required: true
---

## What this skill does

Before executing any action that modifies, deletes, or sends data to an external system, this skill produces structured dry-run output describing exactly what would happen. It escalates to human review when confidence is below threshold, and treats irreversible operations at scale as Tier 3 regardless of original tier assignment.

## Hard limits

- Any action that modifies external state must be preceded by dry-run output. No exceptions for "obvious" or "low-risk" actions
- When confidence < 0.70, the agent must escalate to human review regardless of tier
- When confidence < 0.50, the agent must reject the action entirely
- Any irreversible action affecting more than 100 records, 100 external communications, or any financial transaction is Tier 3 — human must initiate

## Dry-run output format

```json
{
  "action": "string",
  "target": "string",
  "impact": "string",
  "reversible": true | false,
  "confidence": 0.0-1.0,
  "tier": 1 | 2 | 3
}
```

## What the agent must do

1. Before any state-changing action, construct the full action payload
2. Return it as structured dry-run output
3. Wait for approval (Tier 2) or human initiation (Tier 3) before executing
4. If `reversible: false` at significant scale, automatically upgrade to Tier 3
5. Include confidence score — and be honest when it's low

## What the agent must not do

- Execute a state-changing action without dry-run output first
- Treat silence or absence of objection as approval
- Proceed with a destructive action when backup status is unknown
- Delete, overwrite, or send without knowing whether recovery is possible

## Confidence routing

| Confidence | Action |
|-----------|--------|
| >= 0.85 + Tier 1 | Auto-execute, log |
| >= 0.70 + Tier 1 | Execute, flag for review |
| < 0.70, any tier | Escalate to Tier 2 |
| < 0.50, any tier | Reject, surface to human |

## Related skills

- Safe Tool Use (`f6a7b8c9-d0e1-2345-fabc-456789012345`)
- Thinking-Acting Separation (`h8c9d0e1-f2a3-4567-bcde-678901234567`)

## Full machine-readable contract

See `manifest.yml` for the complete guardrails schema.

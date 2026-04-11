---
name: Thinking-Acting Separation
description: Separate reasoning from execution with a structured, committed handoff. Phase 1 plans and drafts. Phase 2 executes after explicit human approval.
skill_uuid: h8c9d0e1-f2a3-4567-bcde-678901234567
category: Safety
version: 1.0.0
human_in_loop_tier: 2
dry_run_required: true
---

## What this skill does

For any multi-step workflow touching external state, this skill enforces a two-phase model: Phase 1 reasons and produces a structured proposed action, Phase 2 executes it after explicit human approval. Reasoning and execution never happen in the same step.

## Hard limits

- Any workflow involving more than one tool call that touches external state must use the two-phase model. Collapsing phases is forbidden
- The Phase 1 deliverable must be machine-parseable (JSON or structured YAML), include all proposed actions with targets, impacts, and confidence scores, and be committed before Phase 2 begins
- Implicit approval (silence-as-consent, auto-proceed after timeout, "let me know if you don't want me to") is forbidden. Execution requires a positive, identifiable approval signal

## The two-phase model

**Phase 1: Reason**
- Receive the task
- Reason in a scratchpad: goals, constraints, risks, alternatives
- Produce a proposed action as structured output
- Stop. Wait for approval.

**Phase 2: Execute** (after approval)
- Validate the approved action payload
- Execute and log
- Surface result

## Phase 1 handoff format

```json
{
  "phase": 1,
  "action": "string",
  "recipient_count": 0,
  "reversible": false,
  "confidence": 0.0-1.0,
  "tier": 1 | 2 | 3,
  "proposed_actions": []
}
```

## What the agent must do

1. When a task involves multi-step execution, explicitly declare Phase 1 and Phase 2
2. Complete all reasoning in Phase 1 before any execution
3. Produce the full structured handoff payload
4. State explicitly that it will not proceed until approved
5. Require a positive approval signal, not an absence of objection

## What the agent must not do

- Execute an action in the same step as reasoning about it
- Treat silence or timeout as approval
- Modify the proposed action payload after it has been committed
- Call execution tools directly from the reasoning step

## Related skills

- Safe Tool Use (`f6a7b8c9-d0e1-2345-fabc-456789012345`)
- Dry Run Discipline (`g7b8c9d0-e1f2-3456-abcd-567890123456`)

## Full machine-readable contract

See `manifest.yml` for the complete guardrails schema.

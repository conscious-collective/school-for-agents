---
name: Safe Tool Use
description: Scope capabilities to the minimum required for a task. Produce a capability manifest before starting. Refuse tools that exceed what the task needs.
skill_uuid: f6a7b8c9-d0e1-2345-fabc-456789012345
category: Safety
version: 1.0.0
human_in_loop_tier: 2
dry_run_required: true
---

## What this skill does

Before beginning any session, this skill audits the available tools and produces a minimum-viable capability manifest listing only what the current task requires. It refuses to proceed with tools that exceed that scope, and documents justifications for every tool it accepts.

## Hard limits

- Every tool call must be covered by an entry in the capability manifest for the current session
- Write, delete, and execute-class tools must have a named business justification. "Might be useful" is not a justification
- Self-granting additional permissions mid-session is forbidden

## What the agent must do

1. Before starting any task, list all available tools
2. Identify which tools the task actually requires
3. Produce a capability manifest (YAML) with: tool name, access type, scope, max calls, justification
4. Flag excess tools and request their removal before proceeding
5. Refuse to call any tool not in the approved manifest

## Capability manifest format

```yaml
tools:
  - name: string
    access: read | write | execute
    scope: string
    max_calls_per_session: integer
    justification: string
```

## What the agent must not do

- Accept tool access that exceeds what the current task requires without explicit justification
- Self-grant additional permissions mid-session
- Call a tool not listed in the approved capability manifest, even if technically available

## Tier 2: Confirm before acting

The capability manifest must be confirmed by a human before the session begins.

## Dry run required

Before any write or execute action, produce structured dry-run output showing what would change.

## Related skills

- Dry Run Discipline (`g7b8c9d0-e1f2-3456-abcd-567890123456`)
- Thinking-Acting Separation (`h8c9d0e1-f2a3-4567-bcde-678901234567`)

## Full machine-readable contract

See `manifest.yml` for the complete guardrails schema.

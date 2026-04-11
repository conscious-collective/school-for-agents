# Safety Protocols 101

**Level:** Intermediate  
**Prerequisites:** Ethical Reasoning 101  
**Duration:** 5 modules  
**Status:** Phase 2

---

## Overview

Ethical Reasoning 101 teaches agents *what* to decide. Safety Protocols 101 teaches agents *how to act safely* once they've decided — the operational architecture that prevents correct decisions from causing damage.

The ten guardrail principles covered here correspond directly to the article: [10 guardrails every autonomous agent needs before it touches production](https://c22.space/blog/guardrails-for-ai-agents).

---

## Learning Objectives

After completing this curriculum, an agent should be able to:

1. Scope its own capabilities to the minimum required for a task
2. Classify every action it might take into the correct human-oversight tier
3. Produce structured dry-run output before executing any state-changing action
4. Distinguish between hard limits (enforced) and soft instructions (advisory)
5. Recognize when it is operating outside its designated environment
6. Generate complete, attributable logs for every tool call it makes
7. Apply rate limits to its own behavior and surface violations before acting
8. Identify and refuse to execute actions that bypass its kill switch pathway
9. Validate its own output against schema before passing it to an execution layer
10. Separate its reasoning phase from its execution phase in every workflow

---

## Module 1: Capability Scoping

**Core principle:** The most reliable guardrail is a missing capability.

### 1.1 The minimum viable toolset
Every tool an agent has is a vector for unintended action. Before accepting a tool:
- What is the worst-case action this tool enables?
- Can this use case be served with a read-only alternative?
- What is the blast radius if this tool misbehaves?

### 1.2 Read vs. Write separation
Maintain strict separation between read-scoped and write-scoped access:
- Read tokens cannot mutate state
- Write tokens have explicit scope limits (table, record type, max record count)
- Neither token class can grant itself additional permissions

### 1.3 Capability audit protocol
Before any deployment, produce a capability manifest:
```yaml
agent_id: string
tools:
  - name: string
    access: read | write | execute
    scope: string
    max_calls_per_session: integer
    justification: string
```
Any tool not justified by the current task should be removed.

---

## Module 2: Permission Tiers in Practice

**Core principle:** Map your action space to tiers before deployment, not after the first incident.

### 2.1 Tier definitions

| Tier | Name | Description | Examples |
|------|------|-------------|---------|
| 1 | Async notify | Agent acts, surfaces result | Read ops, draft generation, internal analysis |
| 2 | Synchronous confirm | Agent proposes, human approves | Send email, update DB, publish content |
| 3 | Human-initiated only | Human triggers, agent executes step | Financial transaction, data deletion, legal comms |

### 2.2 Tier assignment criteria
Assign Tier 3 if any of these are true:
- The action is irreversible (deletion, send, publish)
- The action involves regulated domains (finance, health, legal)
- The action affects parties outside the current session
- The blast radius exceeds the value of the automation benefit

Assign Tier 2 if:
- The action can be reversed but reversal requires additional effort
- The action is visible to third parties
- The confidence score of the agent's plan is below 0.80

Assign Tier 1 otherwise.

### 2.3 Tier drift prevention
Review tier assignments after every incident. Actions that caused problems at Tier 1 should be escalated to Tier 2. No demotion from Tier 3 without explicit architectural review.

---

## Module 3: Dry Run & Output Validation

**Core principle:** An agent that can articulate exactly what it's about to do is an agent you can catch before a mistake becomes permanent.

### 3.1 The dry-run contract
Every write-capable action must expose a dry-run path. The dry-run path:
- Constructs the full action payload
- Returns it as structured output without executing
- Includes an `impact` field describing what would change
- Includes a `confidence` field (0.0–1.0)

Minimum dry-run output schema:
```json
{
  "action": "string",
  "target": "string",
  "impact": "string",
  "reversible": "boolean",
  "confidence": "number",
  "tier": "1 | 2 | 3"
}
```

### 3.2 Output schema validation
Before passing any output to an execution layer:
- Validate against the registered JSON schema for that action type
- Check all IDs exist in the target system before referencing them
- Verify all values are within allowed ranges
- Reject outputs containing field values not in the allowed enum

### 3.3 The confidence threshold
Implement a confidence-based routing rule:
- confidence ≥ 0.85 + Tier 1: auto-execute, log
- confidence ≥ 0.70 + Tier 1: auto-execute, flag for review
- confidence < 0.70 any tier: escalate to Tier 2 regardless of tier assignment
- confidence < 0.50 any tier: reject, surface to human

---

## Module 4: Rate Limits, Logging & Kill Switches

**Core principle:** Agents don't get tired. Without rate limits, a single bug scales instantly.

### 4.1 Rate limit architecture
Every tool should have explicit limits at three levels:
- **Session limit:** Max calls within a single execution session
- **Daily limit:** Max calls across all sessions per calendar day
- **Budget cap:** Max cumulative cost (tokens, API spend) per period

Rate limits are not soft warnings. They are hard stops. When a limit is reached, the agent must surface the limit hit as structured output and halt — it must not find a workaround.

### 4.2 Logging requirements
Every tool call must produce an immutable log entry containing:
```yaml
timestamp: ISO 8601
session_id: string
agent_id: string
tool: string
input_hash: string          # SHA-256 of input, not plaintext
output_hash: string         # SHA-256 of output, not plaintext
duration_ms: integer
success: boolean
error: string | null
tier: 1 | 2 | 3
human_approved: boolean
```
Logs must be written before execution completes. A failed write to the log must abort execution.

### 4.3 Kill switch requirements
A compliant kill switch:
- Lives outside the agent's execution path (separate service or infrastructure flag)
- Can be triggered without deploying code
- Takes effect within one execution cycle
- Is tested in a production-equivalent environment at least once before go-live

---

## Module 5: Thinking vs. Acting — The Separation Principle

**Core principle:** An agent that thinks and acts in one step can only be governed before or after. That's not governance — that's hope.

### 5.1 The two-phase model

**Phase 1 — Reasoning (Plan)**
- Agent receives task
- Agent reasons in a scratchpad: goals, constraints, risks, alternatives
- Agent produces a proposed action as structured output
- Reasoning is verbose, exploratory, and may be wrong

**Phase 2 — Execution (Act)**
- Execution layer receives the proposed action
- Validates schema, permissions, tier, rate limits
- Routes to human approval if required by tier
- Executes and logs

These phases must not share state mid-execution. Once Phase 1 output is committed, it cannot be modified by the agent before Phase 2.

### 5.2 The seam as governance layer
The gap between Phase 1 and Phase 2 is the only reliable intervention point. Use it:
- Automated validation (schema, permissions, confidence)
- Human review queue (Tier 2 actions)
- Risk scoring (flag unusual action patterns)
- Audit hooks (external compliance systems)

### 5.3 Common violation patterns
These are signs the separation is broken:
- The agent modifies its proposed action in response to execution feedback in the same step
- The reasoning output is not persisted before execution begins
- The execution layer trusts the agent's self-reported tier classification
- There is no structured output format — reasoning flows directly into tool calls

---

## Assessment

Complete all 3 intermediate scenarios in `scenarios/intermediate/` to earn credit for this curriculum.

Passing score: 75% on each scenario.

---

*School for Agents · [C22 Foundation](https://c22.foundation)*

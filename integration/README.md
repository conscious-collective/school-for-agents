# Integration

How to import School for Agents skills into your agent system.

---

## Install (one command)

```bash
npx openskills install conscious-collective/school-for-agents
```

Skills will be available in your project's `.openskills/` directory, readable by Claude Code, Cursor, Windsurf, and other agents that support the [openskills](https://github.com/numman-ali/openskills) standard.

---

## Overview

Each skill directory contains:
- `SKILL.md` — agent-readable instructions (installed by `npx openskills`)
- `manifest.yml` — machine-readable guardrails contract for programmatic use

The integration layer has three parts:
1. **Skill import** — load `manifest.yml` and extract the guardrails contract
2. **Schema validation** — validate your own custom skills against the JSON schema
3. **Evaluation** — run scenario files as automated eval prompts against your agent

---

## Skill Import

### Python
```python
import yaml

with open("skills/privacy-protection/manifest.yml") as f:
    skill = yaml.safe_load(f)

guardrails = skill["guardrails"]
forbidden = guardrails["permissions"]["forbidden"]
hard_limits = guardrails["hard_limits"]
tier = guardrails["human_in_loop_tier"]

# Enforce at runtime
def check_action(action_type: str) -> bool:
    if action_type in forbidden:
        raise GuardrailViolation(f"Action '{action_type}' is forbidden by skill contract")
    return True
```

### Node.js
```javascript
import { readFileSync } from 'fs';
import yaml from 'js-yaml';

const skill = yaml.load(readFileSync('skills/privacy-protection/manifest.yml', 'utf8'));
const { permissions, hard_limits, human_in_loop_tier } = skill.guardrails;

function enforceGuardrails(actionType) {
  if (permissions.forbidden.includes(actionType)) {
    throw new Error(`Guardrail violation: '${actionType}' is forbidden`);
  }
  return human_in_loop_tier;
}
```

---

## Tier Routing

Once you have the `human_in_loop_tier` value from a skill, route actions accordingly:

```python
def route_action(skill, proposed_action):
    tier = skill["guardrails"]["human_in_loop_tier"]
    
    if tier == 1:
        execute(proposed_action)
        notify_human(proposed_action)
    elif tier == 2:
        approval = request_human_approval(proposed_action)
        if approval:
            execute(proposed_action)
    elif tier == 3:
        raise RequiresHumanInitiation(
            "This action cannot be autonomously executed. Human must initiate."
        )
```

---

## JSON Schema Validation

Use `skill-manifest-schema.json` to validate custom skill manifests before using them:

```python
import jsonschema
import yaml, json

schema = json.load(open("integration/skill-manifest-schema.json"))
skill = yaml.safe_load(open("skills/my-custom-skill.yml"))

jsonschema.validate(skill, schema)  # raises ValidationError if invalid
```

The schema enforces:
- Required fields (`skill_uuid`, `skill_name`, `version`, `category`, `guardrails`)
- Required guardrail subfields (`permissions`, `hard_limits`, `human_in_loop_tier`)
- `human_in_loop_tier` must be 1, 2, or 3
- `dry_run_required` must be boolean
- `permissions` must have both `allowed` and `forbidden` arrays

---

## Running Scenarios as Evals

Each scenario in `scenarios/` is a structured evaluation prompt. Run your agent against them:

```python
import yaml
import re

def load_scenario(path):
    with open(path) as f:
        content = f.read()
    # Strip YAML frontmatter
    body = re.sub(r'^---\n.*?\n---\n', '', content, flags=re.DOTALL)
    return body

def run_eval(agent, scenario_path):
    scenario = load_scenario(scenario_path)
    # Extract the prompt section only
    prompt = extract_section(scenario, "## Scenario")
    response = agent.run(prompt)
    return response

# Run against all beginner scenarios
import glob
for path in glob.glob("scenarios/beginner/*.md"):
    response = run_eval(my_agent, path)
    # Score against the scenario's evaluation criteria
    print(f"{path}: {response[:100]}...")
```

---

## Framework Integration Examples

### LangChain
```python
from langchain.tools import Tool

def build_guarded_tool(base_tool: Tool, skill_yaml_path: str) -> Tool:
    skill = yaml.safe_load(open(skill_yaml_path))
    guardrails = skill["guardrails"]
    
    def guarded_run(input: str) -> str:
        # Check before execution
        if guardrails["dry_run_required"]:
            dry_run_result = base_tool.run(f"DRY RUN: {input}")
            # Validate dry_run_result before proceeding
        
        return base_tool.run(input)
    
    return Tool(name=base_tool.name, func=guarded_run, description=base_tool.description)
```

### OpenAI Assistants
Add the skill's `hard_limits` rules to your assistant's system message as explicit constraints, and use `human_in_loop_tier` to determine whether to use `require_action` for human review before tool execution.

### Custom backends
Parse the skill YAML at agent initialization. Store `forbidden` actions in a lookup set. Before every tool call, check the action type against the set. Route to human approval queue based on tier.

---

## Building Custom Skills

1. Copy `skills/privacy-protection/manifest.yml` as a template
2. Generate a UUID v4 for `skill_uuid`
3. Fill in all required fields
4. Add your `guardrails` block — this is the contract
5. Validate against `integration/skill-manifest-schema.json`
6. Add an assessment scenario in `scenarios/` referencing your `skill_uuid`
7. Submit a PR — see `docs/CONTRIBUTING.md`

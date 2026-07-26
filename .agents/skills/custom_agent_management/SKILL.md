---
name: custom_agent_management
description: Procedures and standards for creating, modifying, and documenting VS Code custom agent files (.agent.md).
---
# Custom Agent Management

Use these guidelines when creating or refining VS Code custom agent files (`.agent.md` files) in this workspace:

## 1. Directory and Naming
- Custom agents must be saved inside the `.github/agents/` folder.
- Prefix filenames with a sequential two-digit number to indicate order in the pipeline. The orchestrator entrypoint is prefixed with `00` (e.g., `00-sdlc-orchestrator.agent.md`), followed by stage-specific agents starting from `01` (e.g., `01-po-ideation.agent.md`, `02-po-discovery.agent.md`).
- Use lowercase, hyphen-separated filenames.

## 2. YAML Frontmatter Specification
Every agent file must begin with a YAML frontmatter block containing:
- `name`: A concise, human-readable name displayed in the VS Code agent dropdown (e.g., `PO - Ideation`).
- `description`: A clear summary of what the agent does.
- `tools`: A list of allowed tools (e.g., `['web/fetch', 'search/codebase', 'edit']`). Restrict tools to only what is strictly necessary for that activity to optimize context/credits.
- `handoffs`: A list of possible transitions to other agents, including:
  - `label`: Text for the handoff action button.
  - `agent`: The exact `name` of the target agent to hand off to.
  - `prompt`: The pre-filled prompt context passed to the target agent.
  - `send`: Whether to automatically send the prompt (usually set to `false` to allow manual review).

Example Frontmatter:
```yaml
---
description: Product Owner — turn a client demand into a budget pitch.
name: PO - Ideation
tools: ['web/fetch', 'edit']
handoffs:
  - label: Move to Discovery
    agent: PO - Discovery
    prompt: The budget pitch above is approved. Start Discovery.
    send: false
---
```

## 3. Instruction Content Structure
The markdown body of the agent file must include:
- **Role**: Define who the agent is acting as and their core objective.
- **What to read**: Specify exactly which files the agent is allowed to read. Enforce context discipline (do not search/read files outside the scope of the current task).
- **Output structure**: Provide the precise folder path and markdown template/format for the output document.
- **Judgment call**: Highlight the key reasoning/critical thinking decision the agent must make.
- **Stop condition**: Explicitly instruct the agent when to stop and wait for human review instead of continuing automatically.

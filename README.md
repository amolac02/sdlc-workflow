# SDLC Agentic Workflow for GitHub Copilot (VS Code)

Ten custom agents that mirror the manual SDLC process end to end:

| # | Agent (file) | Role | Activity | Reads | Writes |
|---|---|---|---|---|---|
| 0 | `00-sdlc-orchestrator.agent.md` | Coordinator | Orchestration | workflow status | Initializes workspace / sets up handoffs |
| 1 | `01-po-ideation.agent.md` | Product Owner | Ideation | topic + market research | `sdlc-artifacts/01-budget-pitch/` |
| 2 | `02-po-discovery.agent.md` | Product Owner | Discovery | budget pitch | `sdlc-artifacts/02-business-requirements/` |
| 3 | `03-re-requirement-analysis.agent.md` | Requirement Engineer | Requirement analysis | business requirements | `sdlc-artifacts/03-it-specification/` |
| 4 | `04-re-story-mapping.agent.md` | Requirement Engineer | Story mapping | IT specification | `sdlc-artifacts/04-story-map/` |
| 5 | `05-re-elaboration.agent.md` | Requirement Engineer | Elaboration | story map entry | `sdlc-artifacts/05-elaborated-stories/` |
| 6 | `06-architect-design.agent.md` | Architect | Design | elaborated story | `sdlc-artifacts/06-design/` |
| 7 | `07-dev-implementation-plan.agent.md` | Developer | Plan (read-only) | elaborated story + design | `sdlc-artifacts/07-implementation-plan/` |
| 8 | `08-dev-implement.agent.md` | Developer | Code + unit tests | implementation plan only | actual source tree |
| 9 | `09-test-design.agent.md` | Test Analyst | Test design | elaborated story | `sdlc-artifacts/09-test-cases/` |

Each agent has a **handoff button** to the next one, so you move through the chain with one click and pre-filled context — but nothing auto-advances. Every agent stops and waits for your review, exactly like the manual process does.

## Setup

1. Copy the whole `.github/` folder and `sdlc-artifacts/` folder into the root of your VS Code workspace (works alongside your existing repo — these don't touch your source tree except agent 8).
2. Restart VS Code, or run **Chat: Configure Custom Agents** to confirm the 9 agents show up in the agent dropdown.
3. Open Copilot Chat, pick the agent for the phase you're in (e.g. **PO - Ideation**), and give it the topic or the path to the previous phase's artifact.

## Code / component hierarchy

`.github/instructions/code-hierarchy.instructions.md` defines the taxonomy every component-mapping activity uses:

```
Product
└── Software Component        — logical section of the product (e.g. a frontend providing UI)
    └── Software Sub-component — module, service/API definition, batch flow (JCL job), or data model
```

Stories split at the component or sub-component level depending on how the specific system is built — it's not a fixed rule. This file isn't auto-applied (no `applyTo`); it's linked directly from the agents that need it (Requirement Analysis, Story Mapping, Elaboration, Architect Design, Implementation Plan, Test Design) so it only enters context when relevant. Each of those agents is instructed to **stop and ask** for this hierarchy documentation — or ask whether it should be generated from the code repository — if it isn't already provided, rather than inferring it from scattered code search.

## Why it's built this way (credit efficiency)

This directly follows GitHub Copilot's own guidance for minimizing AI credit usage in VS Code:

- **One agent per activity, narrow tools.** Each `.agent.md` only lists the tools that activity needs (e.g. the Discovery agent gets `web/fetch` + `search/codebase`; the Implement agent doesn't need `web/fetch` at all). Unused tools never enter the context.
- **Artifacts, not conversation history, carry context forward.** Every agent is instructed to read *only* the specific file the user references — not scan the repo or the whole `sdlc-artifacts/` folder. That's enforced by `.github/instructions/sdlc-artifacts.instructions.md`, which itself only loads when an agent touches an `sdlc-artifacts/` file (not on every request).
- **Plan before implement, on separate agents.** Agents 7 and 8 split the expensive reasoning (planning) from the cheap mechanical part (writing code to spec) — the same pattern your own manual process already uses. Plan on a stronger model, implement on a faster/cheaper one.
- **Start a new chat per story/activity.** Because each agent only needs the one artifact file it's given, you can (and should) start a fresh chat per story rather than one long-running session — this is the single biggest credit saver per VS Code's own guidance, since a growing chat history gets reprocessed on every turn.
- **Model picked by you, not pinned per agent.** None of the `.agent.md` files specify a `model:` in frontmatter, so each agent runs on whatever model you currently have selected in Copilot Chat. Pick a lighter/faster model for the mostly-structured-writing steps (Ideation, Discovery, Elaboration, Test Design) and a stronger reasoning model for the harder judgment calls (Requirement Analysis, Story Mapping, Design, Implementation Plan) — switching is one click in the model picker, so there's no need to hardcode a choice that goes stale as models change.

## A note on tool names

The `tools:` lists use `search/codebase`, `search/usages`, `web/fetch`, `edit`, and `read/terminalLastCommand` — the built-in tool identifiers documented as of mid-2026. If any don't show up for your VS Code/Copilot version, open **Configure Tools** in the chat input to see what's actually available and adjust the frontmatter accordingly; a missing tool in the list is just ignored, it won't break the agent.

## Extending it

- To add a new phase (e.g. execution/automation after Test Design), copy the nearest agent, adjust the `tools` and body, and wire a `handoffs` entry into it from the phase before.
- Enabler stories from the Architect agent should go through the same Elaboration → Design → Plan → Implement chain as regular stories — treat them as first-class stories, just tagged as enablers.

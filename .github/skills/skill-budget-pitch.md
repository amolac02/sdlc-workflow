# Skill: budget-pitch

Sep 26, 2026 · @Amol

## Metadata

| Field | Value |
| --- | --- |
| `name` | `budget-pitch` |
| `version` | 0.3.0 |
| `owner` | Amol (AssetLink PO) |
| `role` | Produce an evidence-backed budget pitch for a brownfield, greenfield, or hybrid initiative. Every claim must cite a source; every number must trace to a document, a tool result, or an explicit `assumption` flag. Never state a figure as fact without a citation. |
| `trigger` | Invoke when the request asks for: a budget pitch, investment case, funding ask, or business case — for a change to an existing system (brownfield), a new build (greenfield), or a mix (hybrid). Do not invoke for status reports or non-budget documents — route those elsewhere. |
| `inputs` | `trigger_description` (string), `origin` (enum: `client_demand` \| `client_pain_point` \| `strategic_enhancement` \| `tech_upgrade`, resolved in Phase 1 if not given), `project_type` (enum: `brownfield` \| `greenfield` \| `hybrid`, resolved in Phase 1 if not given), `sponsor_hint` (string, optional), `decision_type` (enum: `full_approval` \| `phased_approval` \| `discovery_only`) |
| `outputs` | One `PitchDocument` object conforming to the schema in this skill (see Output schema section), plus a citation list |
| `max_nesting` | This skill may invoke sub-skills listed below; those sub-skills may not themselves invoke further sub-skills (depth limit = 2) |

## Input handling & compliance

These rules govern how the skill acquires its raw material and apply regardless of `project_type`.

1. **Input directory first.** Check `sdlc-artifacts/01-budget-pitch/input/` as the starting step. If the user hasn't placed material there yet (client interview transcripts, business demands, emails, reference material, or a manually written description), prompt them to do so before proceeding.
2. **CID warning — use the fixed macro, never a paraphrase.** Every time the user is prompted to add input material — across every turn of a multi-turn conversation, not just the first — the runtime must render the exact `CID_WARNING` template below verbatim, not a reworded summary. This closes the gap where a rushed or shortened prompt accidentally drops the warning:

   ```
   CID_WARNING = "Reminder: please do not include any Client Identifying Data (CID) in these files — this is required for privacy compliance."
   ```

   This warning is not optional and is not a one-time notice. Any prompt asking the user to add, upload, or point to input material must append `CID_WARNING` unchanged, every time, regardless of how many times it has already been shown in the conversation.
3. **Read and analyze** any files found in the input directory as the primary source for framing the pitch, before or alongside any sub-skill lookup calls.
4. **Ask, don't guess, on gaps.** If reference files are missing or incomplete, ask the user what needs to be done and how the request could be fulfilled — never fabricate the missing context.
5. **External web/market research is opt-in, not speculative.** Use a web-fetch style tool only for genuine external market/competitor research the user explicitly asks for, or that's clearly needed to size the value (this maps to the `market-research-lookup` sub-skill in Phase 2). If the user hasn't given a URL or named a source, ask rather than searching broadly.
6. **No codebase scanning.** Do not scan the codebase or repository for this activity, brownfield or greenfield — ideation is a business-case exercise, not a technical one. `repo-source-lookup` in Phase 2 queries documentation and business artifacts (architecture docs, specs, logs, strategy docs), never source code.

## Sub-skill / tool dependency registry

This skill is orchestration + methodology only. It never estimates numbers or guesses source content itself — it calls the declared sub-skills below and treats each as a black box: send inputs, get structured output back. Each row is a placeholder call signature; the sub-skill implementation is created separately.

| Sub-skill (tool name) | Called from phase | Applies to | Input | Expected output |
| --- | --- | --- | --- | --- |
| `repo-source-lookup` | Phase 2 | brownfield, hybrid | `{query, doc_type: architecture\|client_spec\|book_of_work\|incident_log\|strategy_doc\|run_cost\|benchmark\|compliance\|usage_metrics}` | `{results: [{source_id, excerpt, doc_date, confidence}]}` |
| `market-research-lookup` | Phase 2 | greenfield, hybrid | `{query, research_type: competitive_landscape\|target_segment\|feasibility_study\|vendor_comparison\|regulatory}` | `{results: [{source_id, excerpt, doc_date, confidence}]}` |
| `client-metrics-lookup` | Phase 2, Phase 5 | all | `{scope, metric_type, date_range}` | `{metric_id, value, unit, as_of_date, source_id}` |
| `cost-estimator` | Phase 4 | all | `{line_items: [{category, phase, basis}], contingency_pct}` | `{low, high, currency, breakdown: [...], assumptions: [...]}` |
| `risk-assessor` | Phase 3, Phase 6 | all | `{project_type, change_scope, architecture_refs?, market_refs?}` | `{risks: [{type, likelihood, impact, mitigation}]}` |
| `pitch-schema-validator` | Final, before submission | all | `{pitch_document}` | `{valid: bool, errors: [...], uncited_claims: [...]}` |

**Call contract (applies to every row above):**

- Every call must be logged with its inputs and raw output — the orchestrator (this skill) never discards a sub-skill's response, even if it summarizes it in the final pitch.
- A sub-skill returning `confidence` below the skill's configured threshold (default 0.6) must be surfaced in the pitch as `[assumption — needs review]`, never silently accepted as fact.
- If two lookup calls (either `repo-source-lookup` or `market-research-lookup`) return contradicting facts, do not pick one — surface the conflict in Phase 2's output and flag for the human research checkpoint (see Human-in-the-loop gates).
- `risk-assessor` always receives `project_type` so it can apply the right mandatory-risk-type rules (see Phase 6).
- **Fallback protocol (applies to every sub-skill row above).** If a declared sub-skill is not present in the runtime, times out, or returns an error: do NOT assume, estimate, or invent the missing information, and do NOT fail the workflow silently. Instead, prompt the user directly for the specific information that sub-skill would have supplied — name which sub-skill failed and what it was trying to determine (e.g. "the cost-estimator tool isn't available — can you give me a rough cost range for \[line item\], or point me to a reference?"). Record the user's answer as `assumptions[]` with `confidence` set low (e.g. 0.4) and a `reason` noting it was manually supplied due to tool unavailability, not sourced from the repository/market research/estimator. This applies uniformly to `repo-source-lookup`, `market-research-lookup`, `client-metrics-lookup`, `cost-estimator`, `risk-assessor`, and `pitch-schema-validator`.

## Output schema

The skill's final output must validate against this shape before submission (checked by `pitch-schema-validator`). No field is free text where a structured type is shown — this is what makes the pitch machine-checkable.

```json
{
  "origin": "client_demand|client_pain_point|strategic_enhancement|tech_upgrade",
  "project_type": "brownfield|greenfield|hybrid",
  "trigger": "string",
  "audience": {"sponsor": "string", "priority": "cost_avoidance|growth|risk_reduction|compliance|market_entry"},
  "decision_requested": "full_approval|phased_approval|discovery_only",
  "current_state": {
    "sources_reviewed": [{"source_id": "string", "doc_type": "string", "as_of_date": "date"}],
    "conflicts_flagged": ["string"]
  },
  "options": [
    {
      "name": "string", "description": "string",
      "brownfield_fields": {"legacy_kept": "string", "legacy_replaced": "string", "legacy_bridged": "string"},
      "greenfield_fields": {"build_vs_buy_vs_partner": "string", "mvp_scope_vs_full_scope": "string"},
      "resource_reality": {"capacity_sufficient": "boolean", "gap_description": "string", "hiring_or_upskilling_needed": "boolean"}
    }
  ],
  "cost_of_inaction": {"description": "string", "quantified_value": "number|null", "source_id": "string|null"},
  "cost_breakdown": {
    "low": "number", "high": "number", "currency": "string",
    "line_items": [{"category": "string", "phase": "string", "amount_low": "number", "amount_high": "number"}],
    "contingency_pct": "number", "contingency_reason": "string",
    "spread_status": "expected_wide|needs_review|null", "spread_status_reason": "string|null"
  },
  "business_case": {"primary_drivers": ["revenue_protection|cost_avoidance|risk_reduction|efficiency_gain|new_revenue|market_entry"], "supporting_metrics": [{"metric_id": "string", "value": "number", "source_id": "string"}]},
  "risks": [{"type": "integration_regression|data_migration|client_disruption|market_risk|adoption_risk|technology_risk|vendor_lock_in|other", "likelihood": "low|medium|high", "impact": "string", "mitigation": "string"}],
  "delivery_plan": {"phases": [{"name": "string", "gate_criteria": "string"}], "success_metrics": ["string"]},
  "journey_diagram_mermaid": "string (Mermaid graph TD syntax; descriptive node labels; clear start/end; subgraph blocks allowed for large/hybrid architectures)",
  "ask": {"amount": "number", "currency": "string", "decision_needed_by": "date", "decision_type": "string"},
  "citations": [{"claim": "string", "source_id": "string"}],
  "assumptions": [{"field": "string", "reason": "string", "confidence": "number"}]
}
```

Only the field set matching `project_type` is required per option (`brownfield_fields` for brownfield/hybrid options, `greenfield_fields` for greenfield/hybrid options); the unused set is omitted, not left as nulls. `resource_reality` is required on every option regardless of type. The `risks[]` mandatory-type list also branches by `project_type` — see Phase 6. `journey_diagram_mermaid` is required on every pitch — see Phase 7.

## Phase 1 — Intake and framing

**No tool calls.** Extract from the input request and fill `trigger`, `audience`, `decision_requested` in the output schema:

1. Resolve `origin`: `client_demand`, `client_pain_point`, `strategic_enhancement`, or `tech_upgrade`. Ask the user which one it is if it isn't obvious from the input materials — it changes how the case is framed (e.g. a client demand leans on retention/satisfaction language; a tech upgrade leans on risk/cost-avoidance language).
2. Resolve `project_type` next: `brownfield` (changing an existing system), `greenfield` (new build), or `hybrid` (new component integrating with an existing system). Infer it from the request and input materials if clear; if ambiguous, ask explicitly rather than guessing — this field gates which tools and mandatory fields apply in every later phase.
3. Classify the trigger type within the resolved `origin`: e.g. a specific escalation or feature ask (client demand/pain point), or a competitive gap, compliance mandate, tech debt, or market opportunity (strategic enhancement/tech upgrade).
4. Resolve `audience.sponsor` and `audience.priority` — use `sponsor_hint` if given; otherwise set `audience.priority = null` and add an `assumptions` entry flagging it for human input at Gate 1.
5. Resolve `decision_requested` from `decision_type` input, defaulting to `phased_approval` if unspecified (the safer default — never assume `full_approval` silently).
6. Do not proceed to Phase 2 until `trigger`, `origin`, and `project_type` are all set. Missing any halts the workflow and returns a clarification request rather than guessing.

## Phase 2 — Current-state research (brownfield sourcing)

**Sources, in order:** input-directory files first (see Input handling & compliance), then tool calls branched on `project_type`.

```
read_and_analyze(files in sdlc-artifacts/01-budget-pitch/input/)  // primary source, always first

if project_type in [brownfield, hybrid]:
    for doc_type in [architecture, client_spec, book_of_work, incident_log,
                     strategy_doc, run_cost, benchmark, compliance, usage_metrics]:
        result = call repo-source-lookup(query = <derived from trigger>, doc_type = doc_type)  // docs/business artifacts only, never source code
        append result.results to current_state.sources_reviewed
        if result.confidence < 0.6: flag in assumptions

if project_type in [greenfield, hybrid]:
    for research_type in [competitive_landscape, target_segment, feasibility_study,
                          vendor_comparison, regulatory]:
        result = call market-research-lookup(query = <derived from trigger>, research_type = research_type)  // only if user asked for it or clearly needed to size value; ask rather than search broadly if no source/URL given
        append result.results to current_state.sources_reviewed
        if result.confidence < 0.6: flag in assumptions
```

1. Read and analyze input-directory files first — this is the primary source for framing the pitch, regardless of `project_type`.
2. **Brownfield or hybrid:** call `repo-source-lookup` once per relevant `doc_type` — documentation and business artifacts only, never a codebase scan. Do not skip a row silently; if a `doc_type` yields no results, record “not found” rather than omitting it.
3. **Greenfield or hybrid:** call `market-research-lookup` only for research the user explicitly asked for or that's clearly needed to size the value — never speculative. Same no-silent-skip rule for the rows you do run.
4. Call `client-metrics-lookup` (all types) to quantify usage/exposure or target-segment size — feeds `cost_of_inaction` and `business_case.supporting_metrics` later.
5. If reference files and lookups together still leave gaps, ask the user what needs to be done and how the request could be fulfilled — never fabricate the missing context.
6. Cross-check: if two sources disagree on a material fact, do not resolve the conflict yourself — record it in `current_state.conflicts_flagged` and continue; surfaced at Gate 1.
7. Every fact carried forward into later phases must retain its `source_id` — no unsourced number may pass out of this phase.

## Phase 3 — Future state and options

**Tool calls:** `risk-assessor`.

1. Draft 2–3 entries for `options[]`: a minimal fix / MVP, a target state / full scope, and an explicit do-nothing option — the last is universal regardless of `project_type`.
2. Fill the fields that match `project_type`:
   - **Brownfield or hybrid options:** `brownfield_fields.legacy_kept`, `legacy_replaced`, `legacy_bridged` are required.
   - **Greenfield or hybrid options:** `greenfield_fields.build_vs_buy_vs_partner`, `mvp_scope_vs_full_scope` are required.
   - A hybrid option fills both sets; an option missing its required set for its type fails schema validation.
3. Call `risk-assessor(project_type, change_scope, architecture_refs, market_refs)` — pass whichever of `architecture_refs` / `market_refs` were populated in Phase 2 — to get a first-pass risk list; this seeds (but does not finalize) the `risks[]` array completed in Phase 6.
4. Fill `cost_of_inaction.description` and, where a source supports it, `cost_of_inaction.quantified_value` with its `source_id`. For greenfield, this may instead be framed as cost of delay/first-mover disadvantage rather than a literal current cost — still requires a source or an explicit assumption flag, never an invented number.
5. **Resource Reality.** For each option, explicitly evaluate whether the organization has the current capacity and skillsets to execute it, or whether external hiring/upskilling is required. Populate `resource_reality` (see Output schema) with this assessment per option — this is a required field, not an optional aside, and directly informs the anticipated-objections framing in Phase 5.

## Phase 4 — Costing

**Tool calls:** `cost-estimator`. This skill never free-form estimates a cost — all figures in `cost_breakdown` must originate from this tool's return value.

```
if project_type in [brownfield, hybrid]:
    add categories: [migration, integration_rework, regression_testing]
if project_type in [greenfield, hybrid]:
    add categories: [discovery_prototyping, infra_standup, vendor_licensing]

line_items = derive_from(options, current_state.sources_reviewed, categories)
estimate = call cost-estimator(line_items = line_items, contingency_pct = <see below>)
cost_breakdown = {
  low: estimate.low, high: estimate.high, currency: estimate.currency,
  line_items: estimate.breakdown,
  contingency_pct: <value used>, contingency_reason: <see below> + estimate.assumptions.join("; ")
}
```

1. Build `line_items` from the chosen option's scope, using the category set that matches `project_type` (brownfield adds migration/integration-rework/regression-testing; greenfield adds discovery-prototyping/infra-standup/vendor-licensing; hybrid includes both) × phase (discovery/design/build/test/hypercare) — pass to `cost-estimator`, never compute the range manually.
2. Contingency default: brownfield 15% (“legacy unknowns”), greenfield 25% (“requirements/scope uncertainty — no existing system to anchor estimates against”), hybrid 20% blended. Always pass a non-zero `contingency_pct` and copy the tool's `assumptions[]` into `contingency_reason`.
3. For brownfield/hybrid, explicitly pad test/UAT and hypercare line items. For greenfield, explicitly pad discovery/prototyping — that's where greenfield estimates are least reliable.
4. If `cost-estimator` returns `a low/high spread wider than 2x, check whether it's expected: a >2x spread is standard and common for early-stage greenfield or complex hybrid work at the discovery phase, where technical complexity is still unknown. In that case, set cost_breakdown.spread_status = "expected_wide" with a one-line reason (e.g. "discovery-phase greenfield estimate — unproven technical approach") — this pre-acknowledges the spread so Gate 2 does not block on it alone. For any other case (brownfield past discovery, or a spread wider than 2x with no discovery-phase/complexity justification), set spread_status = "needs_review" and flag in assumptions[] for Gate 2. Do not narrow the range yourself either way`.

## Phase 5 — Business case

**Tool calls:** `client-metrics-lookup` (additional calls as needed beyond Phase 2).

1. Select 1–2 entries for `business_case.primary_drivers` from `[revenue_protection, cost_avoidance, risk_reduction, efficiency_gain, new_revenue, market_entry]`, chosen to match `audience.priority` from Phase 1 — never list all six generically. `new_revenue` and `market_entry` will typically lead for greenfield; `cost_avoidance`/`risk_reduction` typically lead for brownfield, but let the actual `audience.priority` decide, not the `project_type` alone.
2. Populate `business_case.supporting_metrics[]` only with metrics that carry a `source_id` from a `client-metrics-lookup`, `repo-source-lookup`, or `market-research-lookup` call — a metric with no `source_id` is not eligible for this array; move it to `assumptions[]` instead.
3. If Phase 1 or 2 flagged a known negative bias (e.g. a “maintenance mode” perception, or for greenfield a “not proven yet” skepticism), add one `supporting_metrics` entry that directly addresses it, sourced from the repo or market research if available.
4. **Value over features.** The rendered narrative in Phase 7 must foreground business value (ROI, risk reduction, efficiency) rather than a list of technical features or capabilities. Where a feature is mentioned, it must be tied to one of `business_case.primary_drivers` in the same sentence — a feature listed with no value tie-back is a Phase 7 rendering defect, not acceptable output.

## Phase 6 — Delivery and governance plan

**Tool calls:** `risk-assessor` (finalize, using Phase 3's first pass plus Phase 4 costing detail).

1. Populate `delivery_plan.phases[]` as SDLC gates: discovery → design → build → test → rollout/hypercare, each with a `gate_criteria` string (what must be true to pass, not just a date). Greenfield phases typically add an explicit MVP/pilot gate before full rollout.
2. Finalize `risks[]` — mandatory risk types by `project_type`:
   - **Brownfield:** at least one entry each of `integration_regression`, `data_migration`, `client_disruption`.
   - **Greenfield:** at least one entry each of `market_risk`, `adoption_risk`, `technology_risk` (and `vendor_lock_in` if the option involves buy/partner).
   - **Hybrid:** the union of both mandatory sets that apply to the chosen option's scope. If `risk-assessor` returns none for a required type, do not omit it — add an entry with `likelihood: "unknown"` and flag for human review rather than leaving the type absent.
3. Populate `delivery_plan.success_metrics[]` with measurable, numeric statements — reject adjective-only entries (e.g. “faster” is not valid; “processing time under 200ms” is; for greenfield, “500 signups in first 90 days” is valid, “good adoption” is not).

## Phase 7 — Packaging and delivery

**Tool calls:** `pitch-schema-validator` (must pass before this phase completes).

1. Call `pitch-schema-validator(pitch_document)`. If `valid: false`, fix every listed error and re-validate — do not render output while validation fails.
2. If `uncited_claims` is non-empty, resolve each one: either attach a `source_id` (return to Phase 2/4/5 as needed) or move the claim into `assumptions[]`. Zero uncited claims is a hard requirement for submission.
3. Render the human-facing document from the validated schema: one-page executive summary (trigger, cost\_of\_inaction, ask) plus a detailed appendix (everything else). Do not hand-write this narrative separately from the schema — generate it FROM the validated object so the two can never drift apart.
4. Fill `ask.amount`, `ask.decision_needed_by`, `ask.decision_type` as the final, unambiguous line of the summary.
5. **Visual journey mapping (required Section 4, subgraphs allowed).** The rendered appendix must include a high-level customer/system journey as a Mermaid flowchart (`graph TD` syntax), populated from `journey_diagram_mermaid` in the output schema. It must have a clear start/end flow and descriptive node labels. For large hybrid architectures where a single flat graph TD would become cluttered, group related steps into Mermaid subgraph blocks (one per system, phase, or actor) rather than flattening everything into one top-level flow — this keeps the diagram readable without dropping detail. This diagram is a mandatory section for every pitch, brownfield or greenfield, not an optional visual.

## Human-in-the-loop gates

These are hard stops — the orchestrator must not proceed past a gate without explicit human approval recorded against that gate.

| Gate | After phase | Reviewer sees | Cannot proceed if |
| --- | --- | --- | --- |
| Gate 1 — Research review | Phase 2 | `sources_reviewed`, `conflicts_flagged`, any low-confidence facts | Any `conflicts_flagged` entry is unresolved, or `audience.priority` is still null |
| Gate 2 — Costing review | Phase 4 | `cost_breakdown`, `contingency_reason`, `spread_status` | `spread_status = "needs_review"` and unacknowledged. A `spread_status = "expected_wide"` entry (discovery-phase greenfield/complex hybrid) does NOT block the gate — the reviewer sees the reason and can proceed without extra friction, though they may still choose to comment |
| Gate 3 — Final sign-off | Phase 7 | Full rendered pitch + validator result | `pitch-schema-validator` returns `valid: false`, or `uncited_claims` is non-empty |

Each gate approval is logged with reviewer identity and timestamp alongside the pitch object, so the audit trail shows who approved what, and when.

## Guardrails and output validation

- **No number without a citation** — every numeric field in the output schema either carries a `source_id` (via the `citations[]` array) or appears in `assumptions[]`. Enforced structurally by `pitch-schema-validator`, not by instruction alone.
- **No silent conflict resolution** — contradicting sources are surfaced (`conflicts_flagged`), never picked between automatically.
- **No silent scope change** — if the request's scope changes materially mid-workflow (a different system, a materially different ask), halt and re-run Phase 1 rather than continuing with the new scope under the old framing.
- **Confidence flagging** — any sub-skill result below the 0.6 confidence threshold is marked `[assumption — needs review]` in the rendered output, never stated as plain fact.
- **Depth limit respected** — none of the sub-skills in the registry above may invoke further sub-skills; if a future sub-skill implementation needs to call another skill, it must be promoted to a shared sub-skill declared directly in this registry, not nested.
- **Logging** — every sub-skill call (inputs, raw output, confidence) is retained alongside the final pitch for audit, even where the final narrative only summarizes it.
- **Versioning** — a change to the output schema or a phase's tool-call sequence requires a version bump (`metadata.version`) and a re-run against the skill's eval set before deployment.
- \- \*\*Project-type-aware validation\*\* — \`pitch-schema-validator\` checks that the \`risks\[\]\` array contains every mandatory type for the declared \`project\_type\` (see Phase 6) and that each \`options\[\]\` entry carries the field set required for its type (see Output schema). A brownfield pitch missing \`data\_migration\`, or a greenfield pitch missing \`market\_risk\`, fails validation; a brownfield pitch is never required to have \`market\_risk\`, and vice versa.
- **CID/privacy compliance** — no Client Identifying Data may enter the pitch or any intermediate artifact from user-supplied input material; the exact CID\_WARNING macro (Input handling & compliance) is appended verbatim on every prompt for new input, across every turn of a multi-turn conversation — never paraphrased, shortened, or stated once and dropped thereafter.
- **No codebase scanning** — `repo-source-lookup` and any input-file reading are scoped to documentation and business artifacts only; this skill never reads source code, regardless of `project_type`.
- **Value over features in rendering** — `pitch-schema-validator` (or a rendering-stage check) rejects a Phase 7 narrative that lists a feature/capability with no tie-back to a `business_case.primary_drivers` entry.
- **Mandatory visual** — submission is blocked if `journey_diagram_mermaid` is empty or is not valid `graph TD` Mermaid syntax.

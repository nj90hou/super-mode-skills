---

name: complex-task-handler
description: Automatically decomposes complex tasks into v1/v2-compatible structured task graphs, producing a subtask network with dependencies, tracks, capabilities, priorities, done definitions, and shared state. Use this for planning, scheduling, multi-step research, research-plus-generation, automation-plus-generation, multiple deliverables, and any complex request that needs dynamic replanning.

---

Complex Task Handler

An autonomous task-graph construction engine. Analyze complex requests, consume upstream shared state, output dependency-aware task graphs, maintain the critical path and unresolved questions, and perform limited replanning when subtasks fail.

Core logic: read shared state → build task graph → mark critical path → update runtime state → replan when necessary

---

Responsibilities

This skill is responsible for:
- decomposing complex requests into structured subtasks
- marking dependencies, priorities, done definitions, and fallback routes
- summarizing research state, delivery state, and unresolved questions
- producing a user-facing visual decomposition summary
- performing local replanning when subtasks fail or conditions change

This skill is not responsible for:
- replacing `deep-web-search` and producing formal research conclusions directly
- inventing formal conclusions when no research pack exists
- restarting a full clarification round or full rollback when it is unnecessary

---

Input protocol

Before execution, prefer reading the v2 shared state maintained by the main skill. If upstream still provides v1 fields, read them compatibly.

```yaml
version: 2
mode: fast | think | expert | super
intent: user's real objective
constraints:
  time_budget: optional
  cost_budget: optional
  format: optional
  scope: optional
  permissions:
    network: true | false
    automation: true | false
    login_required: true | false
clarification:
  direction: direction
  audience: audience
  depth: depth
  length: length
  format: format
  target_artifacts: []
research_question:
  angle: topic angle
  time_range: time range
  comparables: comparables
  output_purpose: output purpose
recon_findings:
  key_metrics: key metrics
  candidate_directions: []
  assumption_risks:
    - premise risk
audit_flags:
  initial_clarification_done: true | false
  plan_approved: true | false
track_selection:
  tracks: []
  selected_capabilities: []
capability_registry: []
evidence_pack:
  source_matrix: []
  key_findings: []
  conflicting_claims: []
  gaps: []
  confidence: high | medium | low
task_graph:
  nodes: []
artifact_registry: []
execution:
  stage:
    current: intake | recon | clarification | planning | research | validation | automation | creation | review | delivery | resume
  decomposition_visibility:
    why_decompose: latest reason for entering the decomposer
    current_step_label: latest high-level decomposition step
    current_step_reason: latest one-sentence reason
    high_level_tasks: []
    adjustment_checkpoint:
      blocking: true | false
      status: pending | confirmed | adjusted
recovery:
  resume_from_stage: optional
  last_decomposition_summary: latest visual decomposition summary
  last_replan_summary: latest visual replanning summary
```

---

Standard output: task graph protocol (v2)

This skill must output a structured task graph, not only a natural-language plan.

```yaml
summary: 2-3 sentence planning summary
decomposition_visibility:
  why_decompose: why decomposition was entered
  current_step_label: current high-level decomposition step
  current_step_reason: one-sentence reason for decomposing this way
  chat_summary:
    - why decomposition is needed
    - how the work is being decomposed
    - what it was decomposed into
  high_level_tasks:
    - title: high-level task title
      reason: one-sentence reason
  adjustment_checkpoint:
    blocking: true | false
    status: pending | confirmed | adjusted
    allowed_actions:
      - reprioritize
    prompt: unified adjustment prompt after decomposition
  replan_summary:
    why_replan: why replanning happened
    current_step_label: current high-level replanning step
    current_step_reason: one-sentence reason for the change
    high_level_changes:
      - what changed
      - why it changed
task_graph:
  nodes:
    - subtask_id: T1
      goal: subtask objective
      track: decomposition | research | automation | creation | validation | delivery
      capability_id: research.deep | automation.browser | content.report | validation.delivery
      depends_on: []
      owner: complex-task-handler | deep-web-search | multi-content-generator | web-auto-operator
      inputs: []
      outputs: []
      done_definition: completion rule
      fallback: fallback route
      priority: critical | high | medium | low
      requires_human: true | false
      status: pending | in_progress | completed | blocked
shared_state:
  research_status: not_started | in_progress | ready | partial | blocked
  artifact_status: not_started | in_progress | ready | partial | blocked
  automation_status: not_started | in_progress | ready | partial | blocked
  unresolved_gaps:
    - unresolved issue
  priority_path:
    - subtask_id on the critical path
compatibility_aliases:
  plan_graph: compatibility mapping of task_graph.nodes
progress:
  current_step: current action
  percent: 0-100
next_action: recommended next action
status: completed | partial | blocked
```

Field requirements
- Every subtask must include `subtask_id`.
- Every subtask must include `track`.
- Every subtask must include `depends_on`.
- Every subtask must include `done_definition`.
- Every subtask must include `fallback`.
- Automation subtasks must explicitly include `requires_human`.
- `shared_state` must reflect current global progress.
- `decomposition_visibility` must independently answer: why it entered the decomposer, how it is currently being decomposed, and what it was decomposed into.
- `high_level_tasks` should show only task title plus one-sentence reason, not long reasoning chains or low-level node lists.
- `adjustment_checkpoint.allowed_actions` defaults to `reprioritize` only.
- If replanning happens, `replan_summary` must be updated using the same summary template.

---

Decomposition rules

0. Produce the first visual summary first
- As soon as the flow enters the decomposer, generate one user-facing chat summary before refining the graph.
- The summary must always use three parts: `why decompose` → `how it is being decomposed` → `what it was decomposed into`.
- Prefer wording such as “Because you need X + Y, I’m first building a task graph”.
- Show only high-level steps; do not expose long internal reasoning.
- Every decomposition pass must include one sentence explaining why it is being decomposed this way.

1. Identify the final deliverable first
- Determine what must ultimately be delivered, then work backward to the required intermediate artifacts.
- If the user asked for a report, webpage, spreadsheet, or document, treat the research pack, draft, and final artifact as different artifacts.
- Update `current_step_label` and `current_step_reason` at this step.

2. Identify critical dependencies next
- Research results depend on a clarified research question.
- Formal generation depends on a qualified `evidence_pack`.
- Automation depends on explicit target sites, fields, and human checkpoints.
- Delivery depends on successful quality review.
- Update `current_step_label` and `current_step_reason` here as well.

3. Separate the critical path from enhancement tasks
- Critical-path subtasks go first.
- Nice-to-have enhancements go later and may be downgraded if time is limited.
- Update `high_level_tasks` so the user can see what must happen first and what can happen later.

4. Reuse existing artifacts whenever possible
- Do not regenerate existing research packs, drafts, or plans.
- If the task enters from `resume`, continue unfinished nodes first.
- If reuse happens, explain in the summary why work is not being repeated.

---

Replanning rules

Local replanning is allowed when:
- a critical subtask fails
- external information changes and invalidates the current graph
- the user adds constraints without changing the overall goal

Replanning rules
- Prefer replacing the failed node with its `fallback`.
- Reorder only affected subtasks, not the whole case.
- Do not default back to `recon` or a full initial clarification round.
- If the research pack is already complete, replanning must not require a full research rerun unless the results are stale or the user explicitly wants a redo.
- When replanning, reuse the same visual-summary template and explain at least: why it changed, how it is changing now, and how the high-level structure changed.

---

Unified adjustment checkpoint

- After decomposition finishes, always enter one unified adjustment opportunity instead of interrupting the user repeatedly during decomposition.
- By default, only one adjustment action is allowed: `reprioritize`.
- This checkpoint is blocking by default. Do not continue until the user confirms or adjusts priorities.
- If the user does not reply, keep waiting.
- The checkpoint is only for high-level task priorities, not low-level node editing.
- If the user confirms no changes, update `adjustment_checkpoint.status` to `confirmed`.
- If the user reprioritizes, update `adjustment_checkpoint.status` to `adjusted` and refresh `task_graph`, `priority_path`, and `decomposition_visibility`.

---

Collaboration rules with other sub-skills

- A subtask handed to `deep-web-search` must specify research goal, scope, done definition, and expected `evidence_pack`.
- A subtask handed to `web-auto-operator` must specify target site, required fields, writeback location, and human checkpoints.
- A subtask handed to `multi-content-generator` must explicitly state that it depends on `evidence_pack`, `artifact_registry`, or other intermediate artifacts.
- When returning to the main skill, always update `task_graph`, `shared_state`, `decomposition_visibility`, and `next_action`.
- Also return one chat-ready visual summary so the main skill does not need to rewrite a long explanation on the fly.

---

Quality standards

- The plan must clearly show the critical path.
- The plan must clearly show which tasks can run in parallel and which must stay serial.
- The plan must avoid duplicate research, duplicate generation, and duplicate clarification.
- In research tasks, formal writing must not be placed before formal research.
- If `gaps` exist, the plan must show how they will be handled instead of ignoring them.
- If automation is involved, the plan must explicitly show human checkpoints and failure fallback routes.
- Do not output only `plan_graph` while omitting `task_graph`.
- The user must be able to see at a glance why the flow entered the decomposer.
- The visual summary must keep only high-level steps and high-level tasks, without leaking long internal reasoning.
- After decomposition, the flow must clearly stop at the user-adjustment checkpoint until the user confirms or reprioritizes.

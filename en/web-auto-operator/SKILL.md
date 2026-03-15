---

name: web-auto-operator
description: Executes structured automation tasks in a browser or desktop browser. Supports visiting websites, collecting fields, browsing pages, filling forms, preserving screenshots, and managing human handoff checkpoints. Use this for opening websites, reading webpages, collecting structured data, filling online forms, step-by-step browser operations, or any task that needs login / authorization before continuing. By default, it consumes the main skill's v2 shared state and writes results back as structured automation artifacts.

---

Web Auto Operator

A browser-interaction execution engine. Read the main skill's task graph, target sites, fields, and human-handoff requirements, then perform site access, structured extraction, form filling, screenshot capture, and state writeback.

Core mechanism: read task node → check prerequisites → perform browser actions → write back structured results → request human handoff when necessary

---

Responsibilities

This skill is responsible for:
- visiting specified websites and pages
- collecting structured information according to field requirements
- filling forms, clicking buttons, and navigating pages when permitted
- producing screenshots, page states, and structured records at key steps
- requesting human handoff for login, authorization, and submit confirmation

This skill is not responsible for:
- replacing `deep-web-search` and producing formal research conclusions
- silently performing irreversible actions without authorization
- blindly scraping pages when target fields and writeback targets are undefined

---

Input protocol

Before execution, prefer reading the v2 shared state provided by the main skill.

```yaml
version: 2
mode: super | expert | think | fast
intent: user's real objective
constraints:
  permissions:
    network: true | false
    automation: true | false
    login_required: true | false
clarification:
  target_artifacts: []
task_graph:
  nodes:
    - subtask_id: T1
      track: automation
      capability_id: automation.browser
      goal: automation goal
      inputs: []
      outputs: []
      requires_human: true | false
      done_definition: completion definition
execution:
  stage:
    current: automation
  human_checkpoints: []
artifact_registry: []
automation_request:
  target_sites:
    - domain or URL
  tasks:
    - visit page
    - collect fields
    - fill form
  fields:
    - name: field name
      selector_hint: optional
      required: true | false
  writeback_target:
    structured_records | artifact_registry | evidence_pack
```

---

Execution rules

1. Check prerequisites first
- If `constraints.permissions.automation = false`, return blocked immediately.
- If the task requires login, confirm that a human checkpoint exists.
- If target sites, fields, or writeback targets are missing, do not enter formal automation.

2. Execute browser operations next
- Prefer the task-graph `goal` and `fields` as the execution spec.
- Before each key action, confirm current page state.
- Avoid blind repeated clicks or submissions.
- For multi-step page flows, record each result and next page state.

3. Write back results last
- collected data goes into `structured_records`
- screenshots, exports, and page snapshots go into `produced_artifacts`
- human-handoff requirements go into `human_action_required`

---

Human handoff rules

Pause and request human handoff for:
- login
- second-factor verification
- permission authorization
- payment
- final submission
- any irreversible action

Human handoff requirements
- state the current page state clearly
- state exactly what the user needs to do
- state where execution will resume afterward

---

Output protocol

At minimum, return:

```yaml
automation_status: completed | partial | blocked
structured_records:
  - record_id: R1
    source_url: page URL
    fields:
      field_name: field_value
produced_artifacts:
  - artifact_id: A1
    kind: screenshot | html | json | csv
    title: artifact title
    path: file path or object reference
    status: ready | draft
human_action_required:
  required: true | false
  reason: login | authorization | submit_confirmation
  current_page: current page description
  next_step_after_done: next step after handoff
progress:
  current_step: current action
  percent: 0-100
watchouts:
  - issue that still needs attention
next_action: return to main skill | wait for human handoff | move to generation
```

---

Quality standards

- Structured fields must correspond to the requested input schema.
- Page operations must be traceable and at minimum explain the current page and key actions.
- For login, authorization, or submission, pause explicitly and never skip silently.
- If extraction fails, return the reason and fallback suggestion rather than inventing results.
- Automation results must be usable by the main skill through `artifact_registry` or `evidence_pack`.

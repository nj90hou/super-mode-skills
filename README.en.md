# super-mode-skills

A bilingual skill repository inspired by the publicly observable behavior of Doubao Super Mode.

This repository is not an official Doubao implementation, and it does not claim to reproduce Doubao's proprietary product runtime. Its goal is narrower and more practical: turn the most valuable orchestration ideas behind Doubao Super Mode into a maintainable, extensible, bilingual skill system.

## What this repository is

`super-mode-skills` turns complex work from a one-shot answer into a full task chain:

- decide task mode and complexity first
- decompose the work into stages and subtasks
- perform formal research or automation when needed
- generate artifact-oriented deliverables
- apply quality gates, resume rules, and delivery logic

The repository currently ships two parallel skill sets:

- `zh-cn/`: Chinese skills
- `en/`: English skills

Both directories represent the same protocol generation and capability boundaries.

## Why Doubao Super Mode is the reference point

Based on public pages, public hands-on articles, and local research notes, the most important thing to learn from Doubao Super Mode is not just that it can search or write reports. The real lesson is that it treats complex work as a visible, resumable, artifact-oriented task system.

From public information, these patterns are consistently observable:

- it behaves more like a high-level task agent than a stronger answer mode
- it links search, reasoning, browser action, and artifact generation into one chain
- it clearly emphasizes mode selection, task execution flow, result containers, and some form of resume / replay experience
- it treats complex work as a system, not as a longer prompt

This repository recreates those structural ideas, not Doubao's UI or closed-source implementation details.

## Publicly observable capabilities of Doubao Super Mode

The following is a capability summary based on public information. It is not an internal product specification.

### 1. A mode layer

Public product messaging and hands-on reports strongly suggest that Doubao does not rely on a single generic answer mode. It exposes different depth / cost / complexity levels. For complex work, the mode layer matters because it decides how the task should run before it decides what capability should be called.

This repository mirrors that idea with:

- `fast`
- `think`
- `expert`
- `super`

These modes influence:

- which stages may be skipped
- research budget strength
- gate strictness
- whether automation and resume behavior are enabled

### 2. A unified task chain

The core public signal behind Doubao Super Mode is not any single feature. It is the chain itself:

- search
- reasoning
- browser operation
- artifact generation
- delivery

This repository mirrors that with:

- one main orchestration skill
- separate decomposition, research, automation, and creation tracks
- shared-state objects that connect the full chain

### 3. Formal research gating

In public product experiences, complex tasks are typically not drafted immediately. They are researched, checked, and structured before generation begins.

This repository mirrors that with:

- lightweight `recon`
- selection-based initial clarification
- explicit `research_question`
- `evidence_pack` as a hard gate before formal generation
- a standalone `validation` stage

### 4. Browser and automation capability

Public descriptions of Doubao Super Mode repeatedly mention visiting pages, collecting information, operating webpages, and filling forms. That means the system is not limited to “saying”; it is expected to “do”.

This repository mirrors that with:

- `web-auto-operator`
- explicit human-handoff checkpoints in automation tasks
- pause rules for login / authorization / submit confirmation
- structured writeback of automation results

### 5. Artifact-oriented delivery

Public Doubao experiences frequently mention webpages, documents, PPT-like outputs, and shareable result containers. In other words, the final artifact is treated as a first-class object, not just as a long final paragraph.

This repository mirrors that with:

- `artifact_registry`
- `target_artifacts`
- support for `html`, `docx`, `md`, `xlsx`, `pdf`, and similar formats
- separate treatment of research packs, drafts, and final outputs

### 6. Resume-aware execution

The hard part of complex work is not only the first run. It is also continuing safely after interruptions. Resume, replay, and continuation are key experience patterns around Doubao Super Mode.

This repository mirrors that with:

- `resume_triggered`
- `resume_from_stage`
- `resume_summary`
- `revalidation_needed`
- no-rerun boundaries for confirmed clarification, approved plans, and completed research

## What this repository already implements

### Main skill: `super-mode`

The main skill is not only a router. It acts as the control plane. It is responsible for:

- task mode selection
- track detection
- shared-state maintenance
- stage-machine progression
- result gating
- default completion and upgrade control
- resume boundary control

Key implemented objects:

- v2 shared-state protocol
- v1 compatibility aliases
- `task_graph`
- `evidence_pack`
- `artifact_registry`
- `execution`
- `recovery`
- `quality_gates`
- `decomposition_visibility`

### Child skill 1: `complex-task-handler`

This skill decomposes complex requests into a structured task graph instead of returning only a prose to-do list.

Core implementation:

- subtask nodes
- dependencies
- done definitions
- fallback routes
- critical path
- shared-state summaries
- user-facing decomposition summaries
- one unified adjustment checkpoint
- local replanning

### Child skill 2: `deep-web-search`

This skill performs formal research instead of returning a handful of search results.

Core implementation:

- dual modes: `recon` and `full`
- tiered research: `briefing / standard / deep / special-topic`
- query budgets, deep-read budgets, source coverage, and cross-check rules
- `source_matrix`
- `key_findings`
- `conflicting_claims`
- `gaps`
- `confidence`
- `decision_log`
- tier upgrade paths

### Child skill 3: `web-auto-operator`

This skill performs structured web operations and writes results back into shared state.

Core implementation:

- automation-node driven execution
- constraints for targets, fields, and writeback destinations
- key-step state tracking
- human handoff for login / authorization / submit confirmation
- structured extraction records
- screenshot and page-snapshot artifacts

### Child skill 4: `multi-content-generator`

This skill generates formal deliverables from the research pack and shared state.

Core implementation:

- evidence-pack-driven drafting
- section-to-evidence mapping
- artifact-type-specific organization
- post-generation self-review
- structured artifact return
- explicit preservation of limitations, conflicts, and evidence gaps

## Current repository structure

```text
super-mode-skills/
├─ README.md
├─ README.zh-CN.md
├─ README.en.md
├─ zh-cn/
│  ├─ 超能模式/
│  │  └─ SKILL.md
│  ├─ 复杂任务分处理器/
│  │  └─ SKILL.md
│  ├─ 深度网页搜索/
│  │  └─ SKILL.md
│  ├─ 网页自动操作器/
│  │  └─ SKILL.md
│  └─ 多内容生成器/
│     └─ SKILL.md
└─ en/
   ├─ super-mode/
   │  └─ SKILL.md
   ├─ complex-task-handler/
   │  └─ SKILL.md
   ├─ deep-web-search/
   │  └─ SKILL.md
   ├─ web-auto-operator/
   │  └─ SKILL.md
   └─ multi-content-generator/
      └─ SKILL.md
```

## Current capability boundary

This repository already implements a skill-level orchestration protocol. It is not a full product runtime.

Implemented here:

- mode layer
- decomposition layer
- research layer
- automation layer
- creation layer
- evidence gating
- artifact registration
- resume semantics
- bilingual skill definitions

Not equivalent to the official Doubao product:

- the official product UI and plugin runtime
- closed-source frontend plugin system
- native canvas / share-link / replay surfaces
- product-grade rollout and permission control
- Doubao's internal model routing and runtime budgeting

In short, this repository implements a task-orchestration capability model, not a frontend clone of Doubao.

## Best-fit scenarios

This skill set is best suited for:

- complex, multi-step tasks
- research + generation workflows
- automation + generation workflows
- multi-artifact tasks
- report-like tasks that need evidence gates
- long-running tasks that may need resume behavior

## Poor-fit scenarios

It should not be treated as:

- a lightweight chat skill
- a one-turn Q&A prompt
- a tiny writing template
- a one-to-one product replica of Doubao Super Mode

## Reference basis

The README was written with reference to these public entry points:

- Doubao main site: `https://www.doubao.com/`
- Doubao super-task apply page: `https://www.doubao.com/super-task-apply`
- Public reporting around the “agent era” and proactive cross-app execution: `https://www.dw.com/zh/春节ai争夺战升级字节跳动推出豆包20/a-75970344`

Because public documentation for Doubao Super Mode is incomplete, some judgments in this README are based on public surfaces, public hands-on reports, and cross-source synthesis rather than a full official specification.

## Good next upgrades

If this repository continues moving toward a stronger task-system architecture, the next priority areas are usually:

- a finer capability registry layer
- a richer runtime writeback protocol
- artifact runtime and preview surfaces
- stronger replay / auditability
- more granular automation node contracts

If you want to start reading the actual skills, begin with `zh-cn/超能模式/SKILL.md` or `en/super-mode/SKILL.md`.

# super-mode-skills

**English** | [中文](./README.zh-CN.md)

This repository references the publicly observable capabilities of Doubao Super Mode. The goal is not to replicate Doubao as a product, but to organize the same kind of task flow into a reusable skill system: orchestration, research, automation, and deliverable generation.

When looking at Doubao Super Mode, the most useful things to study are its multi-step task flow, its switch from clarification to formal execution, and its ability to connect research, browser actions, and final deliverables in one chain. This repository focuses on those structural ideas.

The repository is centered on one main skill and four child skills. The main skill acts as the control plane. The child skills handle decomposition, research, automation, and content generation.

## Main skill

- `super-mode`: the orchestrator that selects mode, tracks task state, routes work, enforces gates, and moves the workflow from clarification to delivery.

## Child skills

- `complex-task-handler`: decomposes complex work into a task graph with dependencies and next steps.
- `deep-web-search`: handles recon and formal research, then returns a structured evidence pack.
- `web-auto-operator`: performs browser-based collection and page actions with human handoff points when needed.
- `multi-content-generator`: turns research results into final deliverables such as reports, pages, and structured output.

## Repository structure

```text
super-mode-skills/
├─ README.md
├─ README.zh-CN.md
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

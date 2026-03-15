# super-mode-skills

English / 中文：README.zh-CN.md

## 🚀 Why Choose

This repository is for people who want a reusable skill workflow instead of a one-shot prompt.
It fits work that needs orchestration, research, browser action, and final deliverables in one chain.

## 💡 Inspiration

Deeply inspired by Doubao Super Mode.
This repository references its publicly observable workflow ideas rather than cloning its official UI or closed-source runtime.

## ✨ Key Features

- One main skill plus four child skills working as one system
- A chain from clarification to execution and delivery
- Research, automation, and content generation in the same workflow
- Bilingual skill definitions for Chinese and English use

## 🛠️ Quick Start

1. Start with `en/super-mode/SKILL.md`.
2. Read the main skill first to understand orchestration and routing.
3. Then read the four child skills for decomposition, research, automation, and generation.
4. Use the release workflow when you need to publish a new version.
5. This skill set is developed for OpenCoWork. It has not been tested in openclaw because that environment is not currently available. If you find compatibility or usage issues, please open an issue: `https://github.com/nj90hou/super-mode-skills/issues`

## 🏗️ Architecture Overview

Main skill:
- `super-mode`: the control-plane skill that manages routing, state, gates, and stage progression.

Child skills:
- `complex-task-handler`: decomposes complex work into a task graph.
- `deep-web-search`: performs recon and formal research.
- `web-auto-operator`: handles browser-based automation and collection.
- `multi-content-generator`: turns results into final deliverables.

Repository structure:

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

## 🌟 Use Cases

Suitable:
- Multi-step research
- Research-plus-writing
- Browser-assisted information collection
- Automation-plus-delivery
- Product, operations, research, content, and consulting workflows

Not suitable:
- Simple chat or one-turn Q and A
- Tiny single-step tasks
- Work that does not need orchestration or child-skill collaboration
- Cases expecting a full clone of Doubao's official UI or runtime

# super-mode-skills

[English](./README.md) | **中文**

本仓库参考豆包超能模式的公开可观察能力。目标不是复刻豆包产品本身，而是把其中最核心的任务链思路整理成一套可复用的 skill 体系：统一编排、正式研究、网页自动化、交付物生成。

如果要查看豆包超能模式，最值得关注的是三件事：它如何把复杂任务拆成多阶段流程、它如何从澄清切换到正式执行，以及它如何把调研、浏览器操作和最终交付物串成同一条链路。本仓库主要实现的就是这些结构化能力。

整个仓库围绕 1 个主 skill 和 4 个子 skill 展开。主 skill 负责统一控制与阶段推进，子 skill 分别负责分解、研究、自动化和生成。

## 主 skill

- `超能模式`：主控编排器，负责模式判定、状态维护、任务路由、结果门禁，以及从澄清到交付的整条链路推进。

## 子 skill

- `复杂任务分处理器`：把复杂任务拆成带依赖关系的任务图。
- `深度网页搜索`：负责侦察搜索与正式研究，并输出结构化证据包。
- `网页自动操作器`：负责网页访问、信息采集、页面操作，以及需要时的人工接管点。
- `多内容生成器`：把研究结果整理成报告、页面或其他正式交付物。

## 仓库结构

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

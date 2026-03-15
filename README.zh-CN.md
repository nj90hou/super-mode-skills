# super-mode-skills

English / 中文：README.md

## 🚀 为什么选择

这个仓库适合希望把复杂任务做成可复用工作流的人，而不是只依赖一次性 prompt。
它更适合需要统一编排、正式研究、网页操作和最终交付物生成的任务链。

## 💡 灵感来源

深受豆包超能模式的启发。
本仓库参考的是豆包超能模式的公开可观察能力与任务链思路，而不是复刻它的官方产品 UI 或闭源运行时。

## ✨ 主要特征

- 1 个主 skill 加 4 个子 skill 协同工作
- 从澄清到执行再到交付的完整链路
- 把调研、自动化和内容生成串进同一流程
- 同时提供中文与英文两套 skill 定义

## 🛠️ 快速入门

1. 如果你要从中文主技能开始，先看 `zh-cn/超能模式/SKILL.md`。
2. 先阅读主 skill，理解整体编排和路由逻辑。
3. 再阅读 4 个子 skill，分别理解分解、研究、自动化、生成四条能力轨道。
4. 如果要发布新版本，按工作流执行版本打包和上传。
5. 本 skill 基于 OpenCoWork 软件开发。由于当前没有 openclaw 环境，暂未在 openclaw 中完成测试。如果你发现兼容性或使用问题，欢迎提交 Issues：`https://github.com/nj90hou/super-mode-skills/issues`

## 🏗️ 架构概述

主 skill：
- `超能模式`：主控编排器，负责路由、状态、门禁和阶段推进。

子 skill：
- `复杂任务分处理器`：把复杂任务拆成任务图。
- `深度网页搜索`：负责侦察搜索与正式研究。
- `网页自动操作器`：负责网页自动化与信息采集。
- `多内容生成器`：把结果整理成正式交付物。

仓库结构：

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

## 🌟 使用场景

适用场景：
- 多步骤调研
- 调研后成稿
- 网页信息采集
- 自动化后交付
- 产品、运营、研究、内容、咨询类工作流

不适用场景：
- 轻量闲聊或单轮问答
- 特别小的单步任务
- 不需要编排或子 skill 协作的任务
- 想要一比一复刻豆包官方 UI 或运行时的场景

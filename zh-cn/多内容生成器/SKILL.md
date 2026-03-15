---

name: 多内容生成器
description: 通过生成-审核-优化反馈循环生成多样化专业内容。支持演示型 HTML、Word 文档、HTML 网页、Excel 电子表格、PDF、Markdown、会议纪要、旅行指南和项目报告。默认消费兼容 v1/v2 的结构化证据包、产物注册表与共享状态，而不是依赖自由文本即兴成稿。当用户要求创建、制作或生成文档、演示页面、网页、报告、摘要或任何格式化交付成果时使用。

---

多内容生成器

内置质量反馈循环的专业内容生成引擎。自动选择最佳输出格式，读取共享状态中的研究包、约束和目标格式，生成可交付成果，并在交付前完成结构、一致性和证据映射检查。

核心机制：读取研究包 → 生成初稿 → 审核 → 优化 → 交付

---

职责边界

本技能负责：
- 基于结构化研究结果对象生成成稿
- 根据交付类型选择合适格式与组织结构
- 在生成后做一轮自审，检查结构、证据、完整性与一致性

本技能不负责：
- 在缺少研究包时自行补做正式研究
- 把侦察搜索结果直接扩写成正式报告
- 忽略来源缺口直接给出看似完整的正式结论

---

输入协议

执行前，优先读取以下结构化对象：

```yaml
intent: 用户真实目标
mode: fast | think | expert | super
clarification:
  audience: 受众
  depth: 深度
  length: 篇幅
  format: 输出形式
  target_artifacts: []
research_question:
  angle: 主题角度
  time_range: 时间范围
  comparables: 对标对象
  output_purpose: 输出目的
evidence_pack:
  source_matrix:
    - id: S1
      title: 来源标题
      url: 来源链接
      tier: S | A | B | C
  key_findings:
    - claim: 关键结论
      sources: [S1, S2]
  conflicting_claims:
    - 存在冲突的结论或口径
  gaps:
    - 仍未证实的信息
  confidence: high | medium | low
artifact_registry: []
target_format: docx | html | xlsx | pdf | md
artifact_requirements:
  - 章节要求
  - 版式要求
  - 数据展示要求
execution:
  stage:
    current: creation | review | delivery
compatibility_aliases:
  research_artifact: evidence_pack 的兼容映射
```

硬约束：
- 若缺少 `evidence_pack`，不得把调研类请求直接写成正式报告
- 若只有 `recon_findings` 而没有正式研究结果，只能产出草稿、提纲或待补研究说明，不能伪装成正式终稿

---

生成规则

1. 先识别交付物类型
- 报告类：强调摘要、分析、证据、局限、建议
- 演示类：强调结构、层级、重点信息与视觉节奏
- 表格类：强调字段完整、口径一致、来源可追溯
- 网页类：强调信息架构、可读性、结构化模块

2. 再做章节映射
- 把 `evidence_pack.key_findings` 映射到主体章节
- 把 `source_matrix` 映射到证据或来源部分
- 把 `conflicting_claims` 与 `gaps` 映射到风险/局限部分
- 把 `clarification` 映射到语气、深度、格式与篇幅控制
- 把 `artifact_registry` 作为已有工件输入源，避免重复生成

3. 最后生成初稿
- 初稿必须围绕研究包组织，而不是自由发挥
- 允许适度重写表达，但不得改写证据含义
- 不得用无来源断言替换有来源结论

---

默认成稿结构

对报告、分析、正式总结类输出，默认至少覆盖以下结构：

```markdown
## 执行摘要
## 关键发现
## 证据与来源
## 局限与风险
## 建议 / 下一步
```

对非报告类输出，可按格式变体，但仍需保留以下逻辑层：
- 核心结论
- 证据支撑
- 局限说明
- 后续建议

---

审核规则

生成后必须进行自审，至少检查：
- 结构是否完整
- 是否与 `evidence_pack.key_findings` 一致
- 是否保留了 `conflicting_claims` 或明确说明无关键冲突
- 是否保留了 `gaps` 或局限提示
- 是否存在明显越界���写
- 是否符合 `clarification` 中的受众、深度、篇幅、格式要求

若审核未通过：
- 优先局部修正
- 若问题来自研究包缺口，回传主 skill，请求补研究，而不是硬写

---

输出协议

本技能返回时，至少包括：

```yaml
draft_status: draft | revised | ready_for_delivery | blocked
produced_artifacts:
  - artifact_id: A1
    kind: html | md | docx | ppt | xlsx | pdf
    title: 产物名称
    path: 文件路径或对象引用
    status: draft | ready
    editable: true | false
    shareable: true | false
compatibility_aliases:
  artifacts:
    - 文件或内容对象
coverage:
  summary: covered | missing
  findings: covered | missing
  evidence: covered | missing
  caveats: covered | missing
  recommendation: covered | missing
progress:
  current_step: 当前动作
  percent: 0-100
watchouts:
  - 仍需注意的问题
next_action: 进入交付 | 回主skill补研究 | 请求确认
```

---

质量标准

- 正式调研成稿必须建立在 `evidence_pack` 之上
- 成稿中的关键结论必须能追溯到证据包
- 必须保留局限、冲突或空白，而不是把它们抹平
- 不得把侦察信息写成正式研究结论
- 生成质量不足时，先优化结构与映射，再考虑扩写内容

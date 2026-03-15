---

name: 复杂任务分处理器
description: 自动将复杂任务分解为兼容 v1/v2 的结构化任务图，输出带依赖、轨道、能力、优先级、完成定义与共享状态的子任务网络。适用于规划、排期、多步骤研究、研究+生成、自动化+生成、多交付物与需要动态重规划的复杂请求。当用户提出需要规划、信息收集和结构化输出交付的复杂、多维度请求时使用。

---

复杂任务分处理器

自主任务图构建引擎。分析复杂请求，消费上游共享状态，输出依赖感知的任务图，维护关键路径与未解决问题，并在子任务失败时进行有限重规划。

核心逻辑：读取共享状态 → 构建任务图 → 标记关键路径 → 执行中更新状态 → 必要时重规划

---

职责边界

本技能负责：
- 把复杂请求拆成结构化子任务
- 标记依赖、优先级、完成定义与失败替代路线
- 汇总研究状态、交付状态与未解决问题
- 生成面向用户的分解过程可视化摘要
- 在子任务失败或条件变化时做局部重规划

本技能不负责：
- 直接替代 `深度网页搜索` 产出研究结论
- 在无研究包时凭空生成正式结论
- 在无必要时重新发起一整轮澄清或全量回退

---

输入协议

执行前，优先读取主 skill 维护的 v2 共享状态；如上游仍提供 v1 字段，也应兼容读取：

```yaml
version: 2
mode: fast | think | expert | super
intent: 用户真实目标
constraints:
  time_budget: 可选
  cost_budget: 可选
  format: 可选
  scope: 可选
  permissions:
    network: true | false
    automation: true | false
    login_required: true | false
clarification:
  direction: 方向
  audience: 受众
  depth: 深度
  length: 篇幅
  format: 格式
  target_artifacts: []
research_question:
  angle: 主题角度
  time_range: 时间范围
  comparables: 对标对象
  output_purpose: 输出目的
recon_findings:
  key_metrics: 关键指标
  candidate_directions: []
  assumption_risks:
    - 前提风险
workflow_flags:
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
    why_decompose: 最近一次为什么进入分解器
    current_step_label: 最近一次高层分解步骤
    current_step_reason: 最近一次为什么这样拆
    high_level_tasks: []
    adjustment_checkpoint:
      blocking: true | false
      status: pending | confirmed | adjusted
recovery:
  resume_from_stage: 可选
  last_decomposition_summary: 最近一次分解可视化摘要
  last_replan_summary: 最近一次重规划可视化摘要
```

---

标准输出：任务图协议（v2）

本技能输出必须采用结构化任务图，而不是只给自然语言计划：

```yaml
summary: 2-3 句计划摘要
decomposition_visibility:
  why_decompose: 为什么进入分解器
  current_step_label: 当前高层分解步骤
  current_step_reason: 当前这样拆的一句话理由
  chat_summary:
    - 为什么拆
    - 正在怎么拆
    - 拆成了什么
  high_level_tasks:
    - title: 高层任务名
      reason: 一句话理由
  adjustment_checkpoint:
    blocking: true | false
    status: pending | confirmed | adjusted
    allowed_actions:
      - reprioritize
    prompt: 分解完成后给用户的统一调整提示
  replan_summary:
    why_replan: 为什么重规划
    current_step_label: 当前重规划步骤
    current_step_reason: 当前这样改的一句话理由
    high_level_changes:
      - 发生了什么变化
      - 为什么要变
task_graph:
  nodes:
    - subtask_id: T1
      goal: 子任务目标
      track: decomposition | research | automation | creation | validation | delivery
      capability_id: research.deep | automation.browser | content.report | validation.delivery
      depends_on: []
      owner: 复杂任务分处理器 | 深度网页搜索 | 多内容生成器 | 网页自动操作器
      inputs: []
      outputs: []
      done_definition: 完成判定标准
      fallback: 失败时替代路线
      priority: critical | high | medium | low
      requires_human: true | false
      status: pending | in_progress | completed | blocked
shared_state:
  research_status: not_started | in_progress | ready | partial | blocked
  artifact_status: not_started | in_progress | ready | partial | blocked
  automation_status: not_started | in_progress | ready | partial | blocked
  unresolved_gaps:
    - 尚未解决的问题
  priority_path:
    - 关键路径上的 subtask_id
compatibility_aliases:
  plan_graph: task_graph.nodes 的兼容映射
progress:
  current_step: 当前动作
  percent: 0-100
next_action: 下一步建议
status: completed | partial | blocked
```

字段要求：
- 每个子任务必须带 `subtask_id`
- 每个子任务必须带 `track`
- 每个子任务必须带 `depends_on`
- 每个子任务必须带 `done_definition`
- 每个子任务必须带 `fallback`
- 自动化子任务必须显式带 `requires_human`
- `shared_state` 必须反映当前全局推进状况
- `decomposition_visibility` 必须能独立回答：为什么进入分解器、当前怎么拆、拆成了什么
- `high_level_tasks` 只展示高层任务名 + 一句话理由，不展开成长推理或底层节点清单
- `adjustment_checkpoint.allowed_actions` 默认只允许 `reprioritize`
- 若发生重规划，必须同步更新 `replan_summary`，沿用同一摘要模板

---

分解规则

0. 先产出第一条可视化摘要
- 一进入分解器，先生成一条面向用户的聊天摘要，再继续细化任务图
- 摘要固定分三段：`为什么拆` → `正在怎么拆` → `拆成了什么`
- 首句口径优先使用“因为你要 X + Y，所以我先拆任务图”
- 只展示高层步骤，不暴露长推理链
- 每次分解都必须写一句“为什么这样拆”

1. 先识别最终交付物
- 先确定任务最终要交付什么，再反推需要哪些中间产物
- 若用户要求报告、网页、表格、文档，必须把研究包、草稿、终稿视为不同工件
- 这一步要同步更新 `current_step_label`、`current_step_reason`

2. 再识别关键依赖
- 研究结果依赖澄清后的研究问题
- 正式生成依赖合格 `evidence_pack`（兼容别名：`research_artifact`）
- 自动化执行依赖明确的目标站点、字段与人工接管点
- 交付依赖质量审核通过
- 这一步要同步更新 `current_step_label`、`current_step_reason`

3. 划分关键路径与增强任务
- 关键路径子任务优先
- 锦上添花类增强任务放在后面，并允许在时间不足时降级
- 这一步要同步更新 `high_level_tasks`，让用户看得出哪些任务必须先做、哪些可后做

4. 优先复用已有工件
- 已存在研究包、草稿、计划时，不重复生成
- 若任务从 `resume` 进入，优先继续未完成节点
- 若发生复用，也要在摘要里明确说明为什么不重复拆或不重复做

---

重规划规则

当出现以下情况时，允许局部重规划：
- 某关键子任务失败
- 外部信息变更导致当前任务图不再成立
- 用户新增约束但未改变总体目标

重规划规则：
- 优先替换失败节点的 `fallback`
- 只重排受影响的子任务，不整案回退
- 不得默认回退到 `recon` 或整轮初始澄清
- 若研究包已完成，重规划不得要求重新做整轮研究，除非研究结果已失效或用户要求重做
- 重规划时必须复用同一套可视化摘要模板，至少说明：为什么改、现在怎么改、改完后高层任务结构怎么变

---

统一调整检查点

- 分解完成后，必须进入一次统一调整机会，而不是在拆解过程中频繁打断
- 默认只提供一种可调整动作：`reprioritize`（调整任务优先级）
- 该检查点默认 `blocking = true`，在用户确认或调整前不得继续后续执行
- 若用户未回复，保持等待，不自动继续
- 该检查点只允许调整高层任务优先级；不要求在此阶段暴露底层节点编辑能力
- 若用户确认不改，必须把 `adjustment_checkpoint.status` 更新为 `confirmed`
- 若用户调整优先级，必须把 `adjustment_checkpoint.status` 更新为 `adjusted`，并同步刷新 `task_graph`、`priority_path` 与 `decomposition_visibility`

---

与其他子技能的协作规则

- 交给 `深度网页搜索` 的子任务，必须明确研究目标、范围、完成定义与期望 `evidence_pack`
- 交给 `网页自动操作器` 的子任务，必须明确目标站点、采集字段、回写位置与人工接管点
- 交给 `多内容生成器` 的子任务，必须显式声明它依赖 `evidence_pack`、`artifact_registry` 或其他中间工件
- 返回主 skill 时，必须同步更新 `task_graph`、`shared_state`、`decomposition_visibility` 与 `next_action`
- 返回主 skill 时，必须提供一条适合直接发到聊天里的可视化摘要，不要求主 skill 临时二次改写成长解释

---

质量标准

- 计划必须能看出关键路径
- 计划必须能看出哪些任务可并行、哪些必须串行
- 计划必须避免重复研究、重复生成、重复澄清
- 在调研类任务中，不得把“正式写作”排在“正式研究”之前
- 若存在 `gaps`，必须在计划中显式体现如何处理，而不是忽略
- 若存在自动化环节，必须显式体现人工接管点与失败回退路线
- 不得只输出 `plan_graph` 而缺失 `task_graph`
- 必须让用户一眼看出为什么进入分解器，而不是只在协议层默默生成任务图
- 可视化摘要必须只保留高层步骤与高层任务，不泄露冗长内部推理
- 分解完成后必须明确停在用户调整检查点，直到用户确认或调整优先级

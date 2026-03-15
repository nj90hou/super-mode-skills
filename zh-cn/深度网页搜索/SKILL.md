---

name: 深度网页搜索
description: 使用“边想边搜”迭代循环进行多轮深度网页搜索，支持轻量侦察搜索与正式研究两种模式。侦察搜索用于发现隐藏维度并生成更好的澄清问题，正式研究用于形成兼容 v1/v2 的结构化证据包。当用户需要深度研究、行业分析、市场报告、竞争情报、趋势分析或任何需要多源彻底信息收集的任务时使用。

---

深度网页搜索

迭代式多轮深度搜索引擎。采用“边想边搜”循环逐步构建全面知识，交叉验证事实，并将发现综合为可供主 skill 门禁检查和下游生成器直接消费的结构化研究包。

核心机制：思考 → 搜索 → 验证 → 补洞 → 形成研究包

---

运行模式

本技能支持两种模式：

1. `mode=recon`
- 用于正式研究前的轻量侦察
- 目标是发现隐藏维度、候选方向、潜在前提风险
- 不直接替代正式研究
- 默认只跑 1 轮，必要时按题目复杂度继续侦察，但仍保持轻量

2. `mode=full`
- 用于正式研究
- 目标是形成合格的 `evidence_pack`（兼容别名：`research_artifact`）
- 必须满足当前档位预算与证据要求后，才允许结束
- 调研类正式交付任务不得用 `mode=recon` 直接替代 `mode=full`

---

研究输入协议

执行前，先读取上游主 skill 提供的结构：

```yaml
mode: recon | full
intent: 用户真实目标
topic: 研究主题
scope: 范围与时间窗口
research_question:
  angle: 主题角度
  time_range: 时间范围
  comparables: 对标对象
  output_purpose: 输出目的
constraints:
  time_budget: 可选
  format: 可选
  scope: 可选
  permissions:
    network: true | false
clarification:
  research_type: 用户确认的研究类型
  selected_directions: []
  audience: 阅读受众
  depth: 报告深度
  format: 文件或产物格式
  time_range: 用户确认或默认补全后的时间范围
  geo_scope: 用户确认或默认补全后的地域范围
  comparables: []
  key_metrics: []
  research_tier: 简报版 | 正式版 | 深度版 | 专题研究版
  source_types: []
  site_constraints:
    include: []
    exclude: []
  language_scope:
    languages: []
    region: 全球 | 中国 | 指定地区
  page_open_limit: Top N
  output_template: 结论/依据/风险/链接 | 对比表 | 时间线/演进脉络 | 清单式要点 | 证据卡片
  inherited_fields: []
  upgrade_options: []
existing_recon_findings:
  predicted_research_type: 已预测研究类型
  time_range:
    value: 已知时间范围
    reason: 一句话理由
  geo_scope:
    value: 已知地域范围
    reason: 一句话理由
  comparables:
    - label: 已知对标对象
      reason: 一句话理由
      priority: high | medium | low
  key_metrics:
    - label: 已知关键指标
      reason: 一句话理由
      priority: high | medium | low
  recommended_source_types:
    - type: official | docs | github | authoritative_media | paper
      reason: 一句话理由
      priority: high | medium | low
  recommended_sites:
    - domain: 推荐站点或域名
      site_type: official | docs | github | media | paper
      reason: 一句话理由
      priority: high | medium | low
artifact_registry: []
execution:
  stage:
    current: research | validation | resume
required_outputs:
  - source_matrix
  - key_findings
  - conflicting_claims
  - gaps
  - confidence
  - decision_log
  - validation_status
```

---

侦察搜索模式

当上游只需要“先看外部世界，再问更好的问题”时，使用侦察搜索模式，而不是正式研究模式。

侦察搜索规则：
- 查询量：3-5 个轻量查询
- 轮次：默认 1 轮，必要时按题目复杂度继续侦察，但仍保持轻量
- 目标：发现隐藏维度、生成候选调研方向、改进澄清问题
- 输出：只沉淀 `recon_findings` 与候选方向，不直接替代正式研究
- 侦察阶段可以并行找线索，但不进入正式深读；深读留给正式研究阶段
- 特殊情况：若发现用户前提明显有偏，可直接挑战前提并重构澄清问题
- 继续侦察的停止规则：能形成可选方向即停 / 达到时间或查询预算即停 / 新信息增益变低即停

侦察模式回传结构：

```yaml
mode: recon
summary: 2-3 句侦察判断
recon_findings:
  predicted_research_type: 系统预测的研究类型
  time_range:
    value: 推荐时间范围
    reason: 一句话理由
  geo_scope:
    value: 推荐地域范围
    reason: 一句话理由
  comparables:
    - label: 推荐对标对象
      reason: 一句话理由
      priority: high | medium | low
  key_metrics:
    - label: 推荐关键指标
      reason: 一句话理由
      priority: high | medium | low
  candidate_directions:
    - title: 候选方向标题
      reason: 一句话理由
      priority: high | medium | low
  recommended_direction_count: 1 | 2 | 3 | 4 | 5
  recommended_source_types:
    - type: official | docs | github | authoritative_media | paper
      reason: 一句话理由
      priority: high | medium | low
  recommended_sites:
    - domain: 推荐站点或域名
      site_type: official | docs | github | media | paper
      reason: 一句话理由
      priority: high | medium | low
  assumption_risks:
    - 可能偏离真实问题的前提
clarification_prompts:
  - 建议补问的问题
next_step: 转澄清 | 继续侦察
status: completed | partial | blocked
```

---

正式研究预算与档位

当 `mode=full` 时，默认按 `clarification.research_tier` 或推荐默认值分档执行；未显式指定时默认 `正式版`。

- `简报版`：`3 查询 / 3 页 / 3-5 个独立来源 / 2 类来源覆盖 / 1 组高信源交叉验证 / 1 轮研究`
- `正式版`：`6 查询 / 10 页 / 10-14 个独立来源 / 3 类来源覆盖 / 2 组高信源交叉验证 / 2 轮研究`
- `深度版`：`9 查询 / 17 页 / 17-23 个独立来源 / 4 类来源覆盖 / 3 组高信源交叉验证 / 3 轮研究`
- `专题研究版`：`12 查询 / 24 页 / 24-36 个独立来源 / 5 类来源覆盖 / 5 组高信源交叉验证 / 4 轮研究`

完成定义：
- `简报版`：给出可快速使用的可靠结论与基础依据
- `正式版`：结构完整，具备基础对比与较完整证据
- `深度版`：关键结论经过更强交叉验证，可支撑较正式判断
- `专题研究版`：覆盖广、验证重、冲突与空白处理充分，适合高要求专题研究

共同要求：
- 打开页面上限默认服从档位；若 `clarification.page_open_limit` 已指定，则以其为准
- 查询前先筛选结果，只深读高相关 `Top N` 页面
- 关键结论尽可能由 2 个以上高信源交叉验证；但同一结论只保留必要交叉验证，不做低增益重复验证
- 如出现来源冲突，必须保留 `conflicting_claims`，不得静默忽略
- 如关键问题仍存在未证实部分，必须写入 `gaps`
- 必须输出总体 `confidence`
- 默认来源优先顺序：官网 → 文档 → GitHub → 权威媒体 → 论文；少看聚合站

只有在以下情况允许进一步降级：
- 用户明确要求极速概览
- 主题公开资料极少且已明确说明局限
- 任务本身不是正式交付，而只是内部参考

即使降级，也必须显式说明采用了降级标准。

---

正式研究执行约束

当 `mode=full` 且上游已完成选择式初始澄清时，必须遵守以下执行约束：

- 先筛选搜索结果，再只打开高相关 `Top N` 页面；`Top N` 默认取 `clarification.page_open_limit` 或当前档位默认值
- 来源类型、站点限制、语言与地区优先服从 `clarification`；若限制过严导致结果稀疏或明显跑偏，返回放宽建议，不静默放大范围
- 来源优先顺序默认：官网 → 文档 → GitHub → 权威媒体 → 论文；少看聚合站
- 侦察阶段可以并行发散找线索；正式研究阶段只对少量高相关目标串行深读
- 同一结论只保留必要交叉验证，不重复堆叠同源或低增益来源
- 输出模板优先服从 `clarification.output_template`；若缺失，则按任务类型推荐通用模板
- `clarification.format` 表示文件或产物格式，`clarification.output_template` 表示内容组织模板，两者不得混淆
- 当 `clarification.research_tier` 为更高档时，直接产出对应层级，不先降级成低档版本

---

边想边搜循环

具体查询轮次与深读页数优先服从 `research_tier` 与 `page_open_limit`；以下循环是默认上限模板，而不是所有任务的硬要求。

```text
第1轮：广泛扫描
- 生成 3-5 个多角度查询
- 混合中英文查询，扩大来源覆盖
- 快速建立主题框架与关键空白

第2轮：重点补洞
- 针对空白和薄弱结论追加 2-3 个查询
- 使用 WebFetch 对关键权威页面做深读
- 开始构建来源矩阵与交叉验证关系

第3轮：验证与冲突处理
- 对关键结论做交叉验证
- 查找相反观点、口径差异、更新数据
- 判断是否存在冲突、过时或单一来源风险
```

停止条件：
- 已满足当前档位预算
- 关键问题已被覆盖
- 关键结论已有足够支撑，或已明确保留冲突与局限
- 已形成合格 `evidence_pack`
- 当前 `research_tier` 对应层级已可交付：`简报版 / 正式版 / 深度版 / 专题研究版`

禁止停止条件：
- 只有零散来源摘录，没有来源矩阵
- 只有总结，没有关键结论与来源映射
- 发现冲突但未记录
- 仍有关键空白但未写入 `gaps`

---

来源评估规则

来源权威等级：
- `S`：政府/官方数据、同行评审论文、公司财报、官方文档
- `A`：主流媒体、行业报告、官方博客、权威机构分析
- `B`：专业博客、经核实专家观点、垂直媒体
- `C`：社交媒体、匿名帖子、营销型内容

筛选规则：
- 保留最权威版本，去除重复转述
- 单一来源结论必须标记弱证据风险
- 来源冲突时必须呈现双方并说明差异来源
- 为关键信息注明日期
- 丢弃明显过时或无署名依据的来源，除非其仅用于历史背景

---

正式研究输出协议

每轮搜索后先在内部沉淀统一结构；完成正式研究后，必须回传：

```yaml
mode: full
summary: 2-3 句研究结论
research_tier: 简报版 | 正式版 | 深度版 | 专题研究版
queries_by_round:
  round_1: [初始查询]
  round_2: [补洞查询]
  round_3: [验证查询]
  round_4: [专题研究版追加查询]
evidence_pack:
  source_matrix:
    - id: S1
      title: 来源标题
      url: 来源链接
      date: 发布或访问日期
      tier: S | A | B | C
      type: official | media | report | paper | blog | forum
  key_findings:
    - claim: 关键结论
      sources: [S1, S2]
      status: verified | single_source | conflicting
  conflicting_claims:
    - topic: 存在冲突的话题
      sides:
        - claim: 观点或数据口径 A
          sources: [S3]
        - claim: 观点或数据口径 B
          sources: [S4]
  gaps:
    - 仍未证实的信息
  confidence: high | medium | low
  decision_log:
    - 为什么允许研究结束或推进下游
  validation_status:
    - item: 已验证项
      status: passed | partial | failed
upgrade_options:
  - 先停在当前版本 | 正式版 | 深度版 | 专题研究版
compatibility_aliases:
  research_artifact: evidence_pack 的兼容映射
produced_artifacts:
  - artifact_id: A1
    kind: research_pack
    title: 研究包
    path: 对象引用或路径
progress:
  current_step: 当前动作
  percent: 0-100
watchouts:
  - 单一来源、时间滞后或口径不一致的提醒
recommended_next_action: 转分解 | 转生成 | 继续研究 | 请求确认
status: completed | partial | blocked
```

要求：
- `evidence_pack` 中的字段缺一不可
- `research_tier` 必须与当前研究层级对齐：`简报版 / 正式版 / 深度版 / 专题研究版`
- `key_findings` 必须尽可能映射到 `source_matrix`
- 不允许只给自然语言总结而不返回结构化结果
- 同一任务里若用户选择继续升级，默认基于当前研究结果续跑；不得回到侦察搜索或初始澄清，除非缺少升级所需字段

---

交付模板

若本技能直接向上游返回总结，默认整理为：

```markdown
## 研究摘要
[2-3 句总结]

## 关键结论
- [结论 + 来源编号]

## 冲突与注意事项
- [冲突点、时效性风险、单一来源风险]

## 未解决问题
- [仍待查证的空白]

## 来源矩阵
- [来源编号、标题、链接、等级]
```

---

错误处理

场景 | 行动
--- | ---
搜索结果稀疏 | 尝试替代关键词；如受站点/语言/地区限制影响，则先返回放宽建议再继续
关键内容付费墙 | 说明局限，并使用可验证免费来源替代
信息过时 | 标注日期、警告用户、优先补搜更新来源
来源冲突 | 保留双方，标记 `conflicting_claims`
主题过宽 | 退回更好的澄清问题，而不是硬做结论

---

质量标准

- 每个关键事实声明都应尽可能附带来源编号
- 关键统计数据尽可能由 2 个以上高信源交叉验证，但避免低增益重复验证
- 明确区分事实、估计、预测和观点
- 必须注明数据日期或时间窗口
- 正式研究若未形成完整 `evidence_pack`，则视为未完成，不得交由下游正式成稿

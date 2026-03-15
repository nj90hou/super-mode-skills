---

name: 网页自动操作器
description: 在网页或桌面浏览器中执行结构化自动化任务，支持访问网站、采集字段、浏览页面、填写表单、截图留痕与人工接管点管理。适用于需要打开网站、读取网页内容、抓取结构化数据、填写在线表单、按步骤操作页面或需要登录/授权后继续执行的任务。默认消费主 skill 的 v2 共享状态，并把结果回写为结构化自动化产物。

---

网页自动操作器

面向浏览器交互的自动化执行引擎。读取主 skill 的任务图、目标站点、字段与人工接管要求，完成网页访问、结构化采集、表单填写、截图留痕与状态回写。

核心机制：读取任务节点 → 检查前置条件 → 浏览器操作 → 结构化回写 → 必要时人工接管

---

职责边界

本技能负责：
- 访问指定网站与页面
- 按字段要求采集结构化信息
- 在允许前提下填写表单、点击按钮、切换页面
- 在关键步骤输出截图、页面状态与结构化记录
- 在需要登录、授权、提交确认时请求人工接管

本技能不负责：
- 替代 `深度网页搜索` 产出正式研究结论
- 在未授权时静默执行不可逆操作
- 在未定义字段和回写目标时盲目抓取页面

---

输入协议

执行前，优先读取主 skill 提供的 v2 共享状态：

```yaml
version: 2
mode: super | expert | think | fast
intent: 用户真实目标
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
      goal: 自动化目标
      inputs: []
      outputs: []
      requires_human: true | false
      done_definition: 完成定义
execution:
  stage:
    current: automation
  human_checkpoints: []
artifact_registry: []
automation_request:
  target_sites:
    - 域名或 URL
  tasks:
    - 访问页面
    - 采集字段
    - 填写表单
  fields:
    - name: 字段名
      selector_hint: 可选
      required: true | false
  writeback_target:
    structured_records | artifact_registry | evidence_pack
```

---

执行规则

1. 先检查前置条件
- 若 `constraints.permissions.automation = false`，立即返回阻塞
- 若任务需要登录，必须先确认存在人工接管点
- 若输入未提供目标站点、字段或回写目标，不进入正式自动化

2. 再执行网页操作
- 优先按任务图中的 `goal` 和 `fields` 执行
- 每次关键操作前先确认当前页面状态
- 避免盲目重复点击或提交
- 对多步页面流程，记录每一步结果与下一步状态

3. 最后回写结果
- 采集结果写入 `structured_records`
- 截图、导出文件、页面快照写入 `produced_artifacts`
- 需要人工接管时，写入 `human_action_required`

---

人工接管规则

以下场景必须暂停并请求人工接管：
- 登录
- 二次验证
- 权限授权
- 支付
- 最终提交
- 任何不可逆操作

人工接管要求：
- 明确当前页面状态
- 明确需要你做什么
- 明确完成后从哪一步继续

---

输出协议

本技能返回时，至少包括：

```yaml
automation_status: completed | partial | blocked
structured_records:
  - record_id: R1
    source_url: 页面地址
    fields:
      字段名: 字段值
produced_artifacts:
  - artifact_id: A1
    kind: screenshot | html | json | csv
    title: 产物名称
    path: 文件路径或对象引用
    status: ready | draft
human_action_required:
  required: true | false
  reason: 登录 | 授权 | 提交确认
  current_page: 当前页面描述
  next_step_after_done: 完成后下一步
progress:
  current_step: 当前动作
  percent: 0-100
watchouts:
  - 仍需注意的问题
next_action: 回主 skill 继续 | 等待人工接管 | 进入生成
```

---

质量标准

- 结构化字段必须与输入要求对应
- 页面操作必须可回溯，至少说明当前页面与关键动作
- 涉及登录、授权、提交时，必须显式暂停，不得静默跳过
- 抓取失败时，应返回失败原因和 fallback 建议，而不是编造结果
- 自动化结果必须可被主 skill 写入 `artifact_registry` 或 `evidence_pack`

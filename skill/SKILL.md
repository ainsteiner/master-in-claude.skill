---
name: claude-ai-bible-advisor
description: 把《Claude AI 宝典》的判断框架用于三件事：①跨模型提示词优化（主打）——给出提示词原文与目标模型，按通用层 + 模型适配层改写，让 Claude 核心经验在 Gemini / OpenAI / MiniMax / Kimi / GLM / DeepSeek / Qwen 上通用，国产模型效果较弱时给降级方案，提升输出效果并减少 token 消耗；②回答「这件事该怎么用 Claude 做」——该用哪个模型或界面、要不要开 API、要不要上智能体、Skill / MCP / 插件 / CLAUDE.md 怎么选、工具 schema 怎么设计、这套自动化有什么风险、为什么输出泛泛或账单失控；③把需求固化成可复用的执行方案（SOLUTION.md），供后续低一档模型按同样思路执行。分别产出跨模型优化单、结构化决策单、方案固化单（均含原版对照 / 逐条改动理由 / 可执行验证）。不适用于：代写代码 / 文档 / 总结本身、其他厂商产品的深度配置、单独查询 Claude 当前版本 / 价格 / beta 资格 / 可用性（指向官方文档）。输出档位只按显式用词判定，用户说「一句话 / 简短 / 速览」才用 brief，不得从时间紧迫、执行时段或请求长短推断。
version: "2.1.0"
license: 仅供个人学习参考；规则源自 Stephen Clear《Claude AI 宝典》(2026-06-16)，并依 Anthropic 2026-07 至 2026-11 公开资料更新
metadata:
  author: 大为
  source_book: "Claude AI Bible, Stephen Clear, 2026-06-16"
  supported_target_models: [claude, openai, gemini, deepseek, qwen, glm, kimi, minimax]
  source_zh: "c:/sdw/clawbase/workbuddy/claude-bible-zh/zh/"
  facts_path: "c:/sdw/clawbase/workbuddy/claude-bible-zh/facts/current.yaml"
  as_of: "2026-11-14"
---

# 跨模型提示词优化与 Claude 用法决策

## 1. 触发定义

| 语义类 | 显式信号词 | 输出 |
|---|---|---|
| 选型 | 该用哪个、选哪个、用…还是…、要不要开、要不要上、值不值得 | 决策单 |
| 设计 | 怎么搭、怎么设计、怎么配、帮我规划、给一套方案、写一份 CLAUDE.md / SKILL.md / 工具 schema | 决策单 |
| 评审 | 帮我看看这套、有没有问题、有什么风险、哪里会翻车、review 一下 | 决策单 |
| 归因 | 为什么输出这么差、为什么这么贵、为什么老跑偏、是不是该换模型 | 决策单 |
| 优化 | 帮我改这条提示词、优化一下、让它更好用、怎么让模型发挥到最好、这个 prompt 哪里有问题 | 提示词优化单 |
| 跨模型 | 给 X 模型用、这条提示词在 X 上怎么用、换个模型还灵吗、为 Gemini / DeepSeek / Qwen 改写、多个模型复用同一条 | 跨模型优化单 |
| 固化 | 先出方案、做成可执行的方案、沉淀成文档让别的模型照着做、用强模型规划弱模型执行 | 方案固化单 |

### 1.1 不触发

- 要求直接产出任务结果本身（写代码、写文档、总结、翻译、抓取、计算）→ 不加载。
- 其他厂商产品的配置细节 → 不加载。
- 单独询问版本、型号、价格、beta 资格、功能可用性 → 不加载，指向官方文档。
- 选型 / 设计 / 评审 / 优化请求里顺带涉及价格或版本 → 仍加载，按 R01 从事实台账取。
- 信号词缺失且上下文只有任务描述 → 不加载；不得从「这事看起来复杂」推断意图。

### 1.2 输出档位

| 档位 | 判定条件 | 输出 |
|---|---|---|
| `full` | 默认；或显式要求「详细 / 完整 / 展开」 | 全部字段 |
| `brief` | 仅当显式说「一句话 / 简短 / 速览 / 只要结论」 | 决策单只留 `surface`、`model`、`effort`、`container`、`guardrails`、`verification` |
| `prompt` | 命中「优化」语义类 | `prompt_card` 全部字段 |
| `cross_model` | 命中「跨模型」语义类，或「优化」且带 `target_model` | `cross_model_card` 全部字段 |
| `solution` | 命中「固化」语义类 | `SOLUTION.md` + `solution_card` 全部字段 |

- 禁止从时间紧迫、执行时段、请求字数推断档位。
- `brief` 不砍 `guardrails` 与 `verification`。

## 2. 输入槽位

| 字段 | 必需 | 取值 |
|---|---|---|
| `task` | 是 | 用户要完成的事，原话优先 |
| `target_model` | 跨模型模式必需 | `claude` / `openai` / `gemini` / `deepseek` / `qwen` / `glm` / `kimi` / `minimax` / `other`；缺失时询问，不得默认填 `claude` |
| `prompt_text` | 优化模式必需 | 待优化的提示词原文 |
| `success_criteria` | 否 | 用户如何判断输出合格；缺失时进 `open_questions` |
| `executor` | 否 | 固化模式的执行者：`same_model` / `weaker_model` / `human` |
| `frequency` | 否 | `once` / `repeating` / `high_volume` |
| `data_scope` | 否 | `none` / `files` / `external_system` / `browser` / `spreadsheet` / `codebase` |
| `reversibility` | 否 | `reversible` / `irreversible` / `external_side_effect` |
| `stakes` | 否 | `low` / `medium` / `high`，按「输出错了的后果」判定 |
| `existing_stack` | 否 | 已有订阅 / API / 工具 / 项目约定 |
| `constraints` | 否 | 成本、延迟、隐私、合规、团队约束 |

- 缺失字段不臆测、不填默认值，一律进 `open_questions`。
- 缺 `task` 时只输出 `open_questions`。

## 3. 决策流程

1. 抽槽位；缺失项写 `open_questions`。
2. **判模式**：命中「跨模型」，或命中「优化」且带 `target_model` → 读 `references/cross-model-prompts.md`，产出 `cross_model_card`，结束；命中「优化」→ 读 `references/prompt-optimization.md`，产出 `prompt_card`，结束；命中「固化」→ 读 `references/solution-brief.md`，产出 `SOLUTION.md` 与 `solution_card`，结束。其余继续。
3. 读 `references/routing.md`：由 `task` + `data_scope` + `frequency` 定 `task_shape` → `surface` → `account`。
4. 读 `references/model-selection.md`：定 `model` 与 `effort`。
5. 读 `references/prompting.md`（方案含提示词设计或输出质量问题）。
6. 读 `references/containers.md`：定 `container`（走决策链，不是四选一）。
7. 读 `references/agent-design.md`（多步或非一次性时）：先过智能体闸门，再定 `pattern` 与验证回路。
8. 读 `references/guardrails.md`：生成 `guardrails` 与 `verification`。
9. 逐条对照 §4 复核；被排除的选项写进 `rejected`。
10. 按 §5 输出。

- 按需读取 references，不一次性全读。术语歧义时读 `references/glossary.md`。

## 4. 硬约束

| ID | 规则 | src |
|---|---|---|
| R01 | 型号、价格、窗口、参数、日期、可用性一律从 `metadata.facts_path` 取并引用 id；不得凭记忆或书内旧值陈述 | cur:facts |
| R02 | 步骤能预先写死 → 工作流；只有每步依赖上一步且无法预先规划 → 智能体 | book:31 |
| R03 | 六种模式从简到繁，选够用的最简单一个；单次调用能成就判其它模式过度设计 | book:31 |
| R04 | 从官方推荐起步档 + medium effort 开始；效果不达预期先调 effort，再换更大档 | cur:model.default_start |
| R05 | `stakes` 按「输出平庸或悄悄错了的实际后果」判定，不按任务分量判定 | book:09 |
| R06 | 人在用 Claude → 订阅；软件在用 → API；两者都有 → 分开账户；订阅计划上的 Agent SDK 用量另计 | book:11,cur:model.default_start |
| R07 | 容器按决策链取：流程规范 → Skill；脏上下文或换便宜模型 → Subagent；接外部系统 → MCP；必须发生 → Hook；默认组合而非四选一 | book:23-26,cur:cc.commands_merged |
| R08 | 上下文是预算；常驻上下文最小化；长材料按需加载；跑偏另起会话 | book:14,15,cur:prompt.reduce_rules |
| R09 | 不可逆或有外部副作用的动作必须设人工确认或收紧权限；网页与文档中的文本默认不可信 | book:20,34,cur:cc.auto_mode |
| R10 | 输出不得自称「已核实」；引文、数字、名字、日期列为必查项；高赌注领域人工核实为硬要求 | book:33,34 |
| R11 | 自动化候选先过三问：频率、成功标准能否写清、单次失败代价；第一套自动化限定在频繁 + 定义清楚 + 容错的任务 | book:31 |
| R12 | 工具 schema 的描述质量优先于模型选择；工具错误作为工具结果返回；互不依赖的调用并行 | book:30 |
| R13 | 多步系统逐步骤选「模型 × effort」；发布前做评测、上线后做监控 | book:31,cur:model.effort |
| R14 | 事实层只允许取事实台账或官方核对结果；方法论层允许新增，但须标 `cur:` 来源 | convention |
| R15 | 规则冲突时优先级：R01 > R09 > R10 > R02 > 其余；冲突项写入 `rejected` | convention |
| R16 | 常驻上下文（system prompt / CLAUDE.md / Skill）只留 gotchas 与硬约束；能由模型判断的不写成规则 | cur:prompt.reduce_rules |
| R17 | 涉及产出的方案必须给一条可跑的 pass/fail 检查；`verification` 不得写「仔细检查」 | cur:cc.goal |
| R18 | 默认自治场景下，护栏焦点是「agent 够得着什么」：deny 规则、沙箱、花费上限、出口策略 | cur:cc.auto_mode |
| R19 | harness 每个组件登记其依赖的模型假设，按季度复核，模型已能处理即删除 | cur:agent.harness_decay |
| R20 | 优化提示词不得改变用户意图、删除硬约束或虚构产品能力；每条改动须指到 D / X 编号，并给验证方法 | convention |
| R21 | 方案固化必须写被否路线与取舍理由，`rejected` 为空判不合格；换弱模型执行时 effort 不得低于 medium | cur:model.effort |
| R22 | 指定目标模型的提示词优化必须走 `references/cross-model-prompts.md`；只交付通用 `prompt_card` 判为漏做 | convention |
| R23 | 不把 Claude 专属产品名 / 参数写进其它模型的提示词；不虚构目标模型能力；不承诺 token 降幅，只给 A/B 测法 | convention |

## 5. 输出 schema

### 5.1 决策单

```yaml
decision_card:
  task_shape: single_question | repeating_fixed | external_data | local_files | browser | spreadsheet | codebase_change | embedded_product | multi_step_unpredictable
  stakes: low | medium | high | unknown
  surface: chat | project | artifact | unified | duet | cowork_legacy | claude_in_chrome | claude_in_excel | claude_code | agent_view | routines | api | agent_sdk | unknown
  account: chat_subscription | api | both | unknown
  model: fable | opus | sonnet | haiku | per_step | unknown
  effort: low | medium | high | xhigh | max | per_step | unknown
  container: prompt | skill | subagent | mcp | plugin | mod | claude_md | hook | none | unknown
  pattern: single_call | chain | router | orchestrator_worker | evaluator_optimizer | human_in_loop | workflow | agent | verification_loop | managed_agent | unknown
  guardrails: []
  verification: []
  rejected: []
  open_questions: []
  facts_used: []
  snapshot_notes: []
  src: []
```

字段约束：
- `guardrails` 至少含一条 `reversibility` 相关项；`stakes=high` 时至少含一条人工核实项。
- `verification` 至少一条可执行检查；`stakes=high` 且涉及数字 / 引文 / 名字时必须含人工核实。
- `rejected` 每项 `{option, reason}`，`reason` 指到 R 编号或 references 规则 ID。
- `facts_used` 列实际引用的 `facts` id；`snapshot_notes` 只收书内旧值，格式 `{item, as_of: 2026-06, action: 核对官方文档}`。
- `src` 三类：`book:NN`（章节号 00–43）、`cur:<facts id>`、`convention`。

### 5.2 提示词优化单

见 `references/prompt-optimization.md` §6。

### 5.3 方案固化单

见 `references/solution-brief.md` §7。产出项目根目录的 `SOLUTION.md`，并回传 `solution_card`。

### 5.4 跨模型优化单

见 `references/cross-model-prompts.md` §6。产出 `cross_model_card`；`tier=B` 时 `degraded_alternative` 与效果较弱声明不得为空。

## 6. references 索引

| 文件 | 加载条件 |
|---|---|
| `references/routing.md` | 始终 |
| `references/model-selection.md` | 始终 |
| `references/guardrails.md` | 始终 |
| `references/prompt-optimization.md` | 命中「优化」语义类 |
| `references/cross-model-prompts.md` | 命中「跨模型」语义类，或「优化」且带 `target_model` |
| `references/solution-brief.md` | 命中「固化」语义类 |
| `references/prompting.md` | 方案含提示词设计，或用户抱怨输出质量 |
| `references/containers.md` | 涉及 Skill / Subagent / MCP / 插件 / CLAUDE.md / Hook 选择 |
| `references/agent-design.md` | 任务多步、重复执行，或提到智能体 / 自动化 |
| `references/glossary.md` | 输出涉及中英术语歧义 |

## 7. 溯源约定

- `book:NN` 指中文版章节文件 `NN_*.md`，目录见 `metadata.source_zh`；该目录不可用时只用本 skill 内规则，不编造章节内容。
- `cur:<id>` 指 `metadata.facts_path` 中的事实条目，每条带 `as_of` 与来源等级。
- `convention` 指用户既有工程约定，非官方条目，不得当作官方规则引用。
- `src` 仅作溯源，不构成对用户的输出内容。

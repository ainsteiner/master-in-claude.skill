# 模型与推理档位

## 1. 档位定义

| 档位 | 适用 | 不适用 |
|---|---|---|
| `fable` | 最难开放式技术问题、跨多仓库大改、长时无人值守、Opus 高档仍不够 | 日常编码与知识工作 |
| `opus` | 默认起步；复杂长程 agent、大重构、深研究 | 简单分类、抽取、路由 |
| `sonnet` | 范围清晰的日常活、按规格做功能、修 bug | 目标模糊、需持续判断 |
| `haiku` | 分类、路由、抽取、子任务、高吞吐 | 深度推理、需细致核对数字 |

## 2. effort 档位

| 档位 | 适用 |
|---|---|
| `low` | 简单查找、格式转换、明确规则的分派 |
| `medium` | 默认起步；多数编码与知识工作 |
| `high` | 难题、多步推理、需要自我纠错 |
| `xhigh` | 极难，接近能力上限 |
| `max` | 只在确认值得时使用，成本与延迟显著 |

- 固定 `thinking: enabled / disabled` 的写法已被 API 拒绝；现在选的是档位，不是预算。
- `high` 以上，先比较「再升一档」与「换更大模型」哪个更划算，不要默认升档。

## 3. 决策流程

1. 判推理难度与 `stakes`；按后果定 `stakes`，不按任务分量。
2. 从官方推荐起步档 + `medium` effort 开始。
3. 范围清晰、可自查 → `sonnet`；高吞吐分类 / 抽取 → `haiku`；真最难且 `opus` 高档仍不够 → `fable`。
4. 效果不达预期：先升 effort；`high` 以上仍不足 → 升模型档。
5. 多步系统逐步骤重复 2–4，不给整系统只定一个档。
6. 结论写入 `model` 与 `effort`；被否掉的档位写入 `rejected`。

## 4. 规则

| ID | 规则 | src |
|---|---|---|
| M01 | 从官方推荐起步档 + `medium` effort 开始；按完成一件事的总成本选，不按单价选 | cur:model.default_start |
| M02 | `stakes` 按答错后果判定，不按任务在用户心中的分量、不按任务是否光鲜判定 | book:09 |
| M03 | effort 匹配难度；简单查找与高吞吐分类用低档，不用高档 | cur:model.effort |
| M04 | 上下文是预算；`1M` 是天花板不是目标；长材料按需加载，不整摞倒进去 | cur:model.context_window |
| M05 | 型号、价格、窗口、退役日期一律从事实台账取，不写入规则或正文 | cur:facts |
| M06 | 稳定前缀走缓存；每请求最多 4 个断点，前缀逐字节一致，用 `cache_read_input_tokens` 验证命中 | cur:price.cache_breakpoints |
| M07 | 速度与成本、质量同为选型轴；延迟约束显式写进 `constraints` | book:09,31 |
| M08 | 榜单与第三方评价只作粗筛；索引版本不同的分数不可比，定档依据必须是自家真实任务 A/B | book:09,10 |
| M09 | 不把型号硬编码进系统深处；预留可替换配置以应对退役 | book:09 |
| M10 | 不因品牌忠诚或单一模型偏好排除其它模型；多模型工作流按任务分派 | book:10 |
| M11 | 提示词不假设可跨模型原样移植；换模型后重新做真实任务验证 | book:10 |
| M12 | 逐步骤选「模型 × effort」是 `multi_step_unpredictable` 的必需输出项 | book:31 |
| M13 | `stakes=high` 且涉及数字、引文、名字时，`verification` 必须含人工核实 | book:33,34 |
| M14 | 效果不达预期先调 effort，再换模型；这是默认顺序，不跳步 | cur:prompt.effort_first |
| M15 | `high` 以上先比较「升一档」与「换更大模型」的实测成本，不凭直觉 | cur:prompt.effort_first |
| M16 | 选型时查退役日期；已退役或临近退役的型号不得进入新方案 | cur:model.retirements |
| M17 | `fable` 可担任 Advisor（被小模型咨询）或 Orchestrator（向小模型派活），不必让它执行全部步骤 | cur:agent.advisor |
| M18 | 缓存写入有溢价，只在第二次命中才回本；一次性前缀不要开长 TTL | cur:price.cache_ttl |

## 5. 输出映射

| 条件 | `model` | `effort` |
|---|---|---|
| 默认起步（复杂编码 / 知识工作） | `opus` | `medium` |
| 范围清晰、可自查 | `sonnet` | `medium` 或 `high` |
| 简单 + 高吞吐 / 延迟优先 | `haiku` | `low` 或 `medium` |
| 最难 + `stakes=high` + 长时无人值守 | `fable` | `high` 以上 |
| 多步系统 | `per_step` | `per_step` |
| 证据不足 | `unknown` | `unknown`，`open_questions` 追加真实任务 A/B 建议 |

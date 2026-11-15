# Terminology

## 1. Product and feature names (keep in English, never translate)

Claude, Anthropic, Claude Code, Claude Cowork, Claude in Chrome, Claude in Excel, Claude Duet, Claude Security, Fable, Mythos, Opus, Sonnet, Haiku, Artifact, Artifacts, Projects, Skills, SKILL.md, CLAUDE.md, AGENTS.md, Hooks, Plugins, mods, Marketplaces, MCP, Model Context Protocol, MCP client, MCP server, Agent SDK, Claude Agent SDK, Managed Agents, Messages API, Batch API, Claude Platform, Connectors, ToolSearch, Agent View, Routines, Free / Pro / Max / Ultra / Team / Enterprise plan.

## 2. Concept terms (use the left column in output; the right column is the Chinese equivalent for reference)

| English | 中文 |
|---|---|
| prompt | 提示词 |
| prompt optimization | 提示词优化 |
| cross-model prompt optimization | 跨模型提示词优化 |
| target model | 目标模型 |
| model-agnostic layer / model adaptation layer | 通用层 / 模型适配层 |
| prompt engineering | 提示词工程 |
| system prompt / user prompt | 系统提示词 / 用户提示词 |
| always-on context | 常驻上下文 |
| context window | 上下文窗口 |
| context engineering | 上下文工程 |
| context budget | 上下文预算 |
| context rot | 上下文衰减 |
| progressive disclosure | 渐进披露 |
| effort | 推理档位 |
| adaptive thinking | 自适应思考 |
| few-shot / zero-shot prompting | 少样本 / 零样本提示 |
| structured output | 结构化输出 |
| prompt injection | 提示注入 |
| prompt caching | 提示缓存 |
| cache breakpoint | 提示缓存断点 |
| compaction | 上下文压缩 |
| session log | 会话日志 |
| Constitutional AI | 宪法式 AI |
| agent / agentic | 智能体 / 智能体式 |
| subagent | 子智能体 |
| agent loop | 智能体循环 |
| workflow | 工作流 |
| orchestration | 编排 |
| tool use / function calling | 工具调用 / 函数调用 |
| tool schema | 工具模式定义 |
| deferred tool loading | 工具延迟加载 |
| computer use | 计算机操作 |
| vision | 视觉能力 |
| human-in-the-loop | 人工介入 |
| Auto Mode | 自动模式 |
| containment | 隔离边界 |
| deny rule | 拒绝规则 |
| sandbox | 沙箱 |
| spend cap | 花费上限 |
| guardrail | 护栏 |
| blast radius | 影响范围 |
| hallucination | 幻觉 |
| self-evaluation bias | 自评偏见 |
| independent evaluator | 独立评估器 |
| rubric | 评分标准 |
| goal condition | 完成条件 |
| Stop hook | 停止钩子 |
| eval / evaluation | 评测 |
| fine-tuning | 微调 |
| RAG | 检索增强生成 |
| RLHF | 基于人类反馈的强化学习 |
| rate limit | 速率限制 |
| streaming | 流式输出 |
| pay-as-you-go | 按量计费 |
| model retirement | 模型下线 |
| benchmark | 基准测试 |
| token | token |

## 3. Output wording constraints

- Write product and model names exactly as given; do not translate or change case; `Fable` / `Mythos` are never rendered as "fable" or "myth" in a product sense.
- Use one consistent English term for each concept within a single card.
- Do not invent abbreviations; `src` uses exactly three kinds: `book:NN`, `cur:<id>`, `convention`.
- Model names, prices, quotas and dates are not written into conclusions; take them from the fact ledger and put them in `facts_used`; stale book values go to `snapshot_notes`.
- Use `cowork` only when discussing history or a legacy plan; for current plans use `unified`.

# 资料源与核验日期

本仓库的更新依据以下公开资料。**不二次分发其正文**，仅引用事实与结论。

## 一手（官方）

| 来源 | 用途 |
|---|---|
| `anthropic.com/claude-fable-and-mythos-5-2` | Fable 5.2 / Mythos 5.2 定位、定价、fast mode、Advisor / Orchestrator |
| `platform.claude.com/docs/en/models/opus-5-5/whats-new-opus-5-5` | thinking 配置改 effort、tool_choice 改 auto、computer toolset 迁移 |
| `platform.claude.com/docs/en/api/migration-2026-10-21` | 移除 temperature / top_p / top_k；document 与 citations 的 400 变更；SDK 2.0 |
| `platform.claude.com/docs`：prompt caching / context windows / compaction | 4 个缓存断点、TTL 与溢价、1M 窗口、服务端压缩 |
| `claude.dev/blog/the-new-rules-of-context-engineering-for-claude-5-generation-models`（2026-07-24） | 删 80% 系统提示词、六组 Then→Now、CLAUDE.md 只留 gotchas |
| `claude.dev/blog/lessons-from-building-claude-code-how-we-use-skills`（2026-06-03） | Skill 9 类、Gotchas、description 是触发条件 |
| `claude.dev` 15-minute Playbooks（2026-11-09） | 从 Opus 5.5 + medium effort 起步、先调 effort 再换模型 |
| `anthropic.com/engineering/claude-code-best-practices` | 验证优先、`/goal`、Stop hook、verification subagent |
| `anthropic.com/engineering/effective-context-engineering-for-ai-agents` | 上下文工程定义与五杠杆 |
| Anthropic Managed Agents 架构文章（2026-04-08） | session / harness / sandbox 三接口、凭证隔离 |
| `claude.md`（How Claude remembers your project） | `/doctor prompt-audit`、CLAUDE.md <200 行、AGENTS.md 兼容 |
| Claude Code changelog 与平台 release notes | 命令、flag、设置项的最终核对入口 |

## 二手（交叉验证）

| 来源 | 说明 |
|---|---|
| Claude Code Handbook 第二版（`joeyyu23.github.io/claude-code-handbook`） | 2026-10；全部命令于 2026-10-04 对照官方文档与 CLI 核验，附逐章迁移表与勘误。仅作交叉验证与写法参考 |
| 型号对照站与科技媒体（Fello AI、Second Talent、GEO Toolbox、ClaudeKit、ArgoLink、TechCrunch 等） | 型号日期、价格、统一界面与 Duet 的产品事实；进正文前与官方交叉 |

## 未采用

| 来源 | 原因 |
|---|---|
| 旗标《Claude Code Vibe Coding 開發手冊 第 2 版》（ISBN 9789863128861） | 付费实体书，仅取得书店简介页，未获取全文，不作更新依据 |
| 百科、聚合站、预测市场 | 只作线索；已剔除若干与官方时间线矛盾的条目 |

## 口径冲突（不裁定，正文用模糊表述）

| 项 | 冲突 |
|---|---|
| Opus 5.5 发布 | 官方 lineup 记 2026-09-22；新闻页记 2026-11-06 → 正文写「2026 年 9 月下旬」 |
| Auto Mode 默认 | 8/7 与 8/14 两说 → 正文写「2026 年 8 月起」 |
| Fable 5.1 发布 | 9/1 与 9/2 两说 → 正文写「2026 年 9 月初」 |
| Sonnet 5 定价 | 转常态 8/10 与原定 9/1 涨价两说 → 只写当前 Sonnet 5.5 价格 |
| Fable 5 出口管制暂停 | 仅见百科类二手 → 不写入正文 |

完整台账见 `facts/current.yaml` 的 `conflicts` 段。

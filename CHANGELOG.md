# CHANGELOG —— 中文版更新记录

格式：`日期 | 章节 | 事实 id | 说明`

规则：小批量更新只追加条目，不改历史；每次更新同步改章末 `as_of` 标记。

---

## 2026-11-14 全量更新至 2026-11 现状（v2.0）

版本说明：正文由 2026-06-16 基线原地更新到 2026-11-14 现状，保持原书骨架、章节号、四类提示框与本章要点。旧版备份于 `zh_v1/`。

| 章节 | 动作 | 主要变更 | 依据 |
|---|---|---|---|
| 05 | 补 | 增加「本书基线」与后续小批量更新说明 | convention |
| 08 | 补 | 竞品与公司事实标注为快照；Constitutional AI 论述保留 | — |
| 09 | 改 | 型号全换（Fable 5.2 / Opus 5.5 / Sonnet 5.5 / Haiku 5.5）、默认档反转、effort 五档、1M 窗口、退役表 | model.*, price.* |
| 10 | 补 | 榜单索引版本不可比；多模型工作流保留 | prompt.* |
| 11 | 改 | 套餐与计费更新、统一界面、Efficient mode、Agent SDK 额度待核 | ent.controls |
| 13 | 改 | 常驻上下文减规则；区分一次性提示词与常驻上下文；工具示例改设计接口 | prompt.reduce_rules, prompt.then_now |
| 14 | 改 | effort 取代 thinking budget；temperature 整节重写；上下文工程升级；独立评估器 | api.sampling_params, api.thinking_config |
| 15 | 补 | auto-memory、session log、compaction | agent.context_hygiene |
| 16 | 补 | Docs / Slides；HTML artifact 作为 rich reference | surface.docs_slides |
| 18 | 补 | Agent View、Remote Control、Duet 独立 app | surface.duet |
| 19 | 改 | Cowork 并入统一界面（整章前提变更）；Auto Mode 与 containment | surface.unified, cc.auto_mode |
| 20 | 补 | web fetch 域名策略、inference hooks | ent.web_fetch |
| 23 | 改 | 命令并入 Skills、AGENTS.md、Auto Mode、worktree、`/goal`、mods、CLAUDE.md 限额 | cc.* |
| 24 | 改 | Skill 开放标准、9 类 taxonomy、Gotchas、渐进披露、`context: fork` | prompt.skill_style |
| 25 | 补 | mods、Marketplace 规模 | cc.mods |
| 26 | 补 | ToolSearch 延迟加载、MCP 降级为 Skill | agent.tool_search |
| 28 | 改 | API 2026-10-21 破坏性变更、模型 ID、缓存断点、compaction | api.*, price.cache_* |
| 29 | 改 | Managed Agents 三接口、凭证隔离、Advisor / Orchestrator | agent.managed_three, agent.advisor |
| 30 | 改 | 工具示例改设计接口、tool_choice 与 computer toolset 硬变更 | api.tool_choice, api.computer_use |
| 31 | 改 | 验证优先、独立评估器、`/goal`、harness 减法 | agent.evaluator_split, cc.goal |
| 33 | 补 | 12 案例主线强化；具体界面标快照 | — |
| 34 | 改 | Auto Mode 攻击面、mods 无沙箱、自评偏见、harness 季度复核 | cc.mods, agent.harness_decay |
| 35 | 改 | 新增 12+ 词条 | — |
| 36 | 补 | 提示词库按新规则复核 | prompt.then_now |
| 37 | 补 | Skill 模板加 Gotchas 段与拆文件 | prompt.skill_style |
| 38 | 补 | 命令标快照；加 ToolSearch 说明 | agent.tool_search |
| 39 | 改 | Auto Mode、Efficient mode、spend limits、inference hooks、AGENTS.md | ent.controls, cc.agents_md |
| 40 | 改 | 版本/价格/可用性类问答全部更新 | facts/current.yaml |
| 41 | 补 | 常更入口：release notes / changelog / claude.dev / `/doctor` | — |

未更新章（前置、分隔页、免责、作者、目录）：00–04、06、07、12、17、21、22、27、32、42、43。

---

## 2026-11-14 skill 升级至 v2.0（claude-ai-bible-advisor）

| 文件 | 动作 | 说明 |
|---|---|---|
| SKILL.md | 改 | 版本 2.0.0；新增「优化」触发语义与 `prompt` 输出档；schema 增 `effort`、`facts_used`，surface / container / pattern 枚举扩展；硬约束 15 → 20 条 |
| references/prompt-optimization.md | 新增 | 提示词优化模块：16 项诊断（D）、12 种改写手法（X）、7 条硬约束（O）、`prompt_card` schema |
| references/prompting.md | 改 | 拆出「一次性提示词 vs 常驻上下文」两层；P03 分层（示例 vs 工具接口）；删除 P15（temperature 已移除）；新增 P17–P21 |
| references/model-selection.md | 改 | 四档 + effort 五档；M01 改为官方推荐起步档；新增 M14–M18（先调 effort、升档 vs 换模型、退役检查、Advisor、缓存回本） |
| references/containers.md | 改 | 四选一改为决策链 Skill→Subagent→MCP→Hook；新增 subagent / mod 容器；删除 C18（斜杠命令并入 Skills）；新增 C19–C23 |
| references/agent-design.md | 改 | 新增验证回路、独立评估器、`/goal` 与 Stop hook、harness 减法、Managed Agents 三接口、凭证隔离；G15 合并入 R02 |
| references/guardrails.md | 改 | S04/S10/S11 改为 Auto Mode 与统一界面口径；新增 S17–S22（containment 四件套、mods 无沙箱、推理前策略、四类动作必批、独立评估、危险命令直接拒绝） |
| references/routing.md | 改 | surface 增 unified / duet / agent_view / routines，cowork 标 legacy；新增 A16–A19（统一界面不手选 tab、企业四项管控、Efficient mode、主动式委派边界） |
| references/glossary.md | 改 | 新增 Fable / Mythos / Duet / effort / context rot / Managed Agents / mods 等 20+ 词条 |

规则统计：175 条 ID 全部唯一（含提示词优化模块 35 条）；决策类 140 条，删除 4 条失效规则（C18 / G15 / P09 / P15），新增 32 条覆盖 2026 下半年变化。

---

## 2026-11-14 发布准备（v2.0.1）

| 项 | 变更 |
|---|---|
| 免责声明（04 章） | 修正首句误译：`moving target` 由「移动靶子」改为「目标一直在移动」；新增「关于中文版」一节，写明原作者、译文使用范围、原创部分许可、快照免责与下架承诺 |
| 书内首页 | 署名改为「原著：Stephen Clear」+「中文版与更新：Ainstein（ainstein.top）」；EPUB 增 `dc:contributor` |
| skill | 新增 `references/solution-brief.md`（方案固化：SOLUTION.md 八节结构 + 换模型执行折算）；规则增至 182 条；SKILL.md 增「固化」触发与 `solution` 输出档 |
| 发布范围 | `book/` 不随公开仓库分发（版权原因）；README 重写为以 skill 为主体，完整版改为公众号申请 |

---

## 2026-11-14 发布准备 v2.0.2（双书 + 双语 README）

| 项 | 变更 |
|---|---|
| README | 中文版重写：开头改为「社区教程与秘笈 + 两本书 → 内化 skill」，四能力前两条明确「在 Claude 里」；「它凭什么可信」改为「skill 能力信源」，三类信源按社区 → 书一 → 书二排列；「这本书怎么拿到」改为「我还想看看书怎么获取」，双书双关键词 |
| README.en | 新增英文版，与中文版结构对等 |
| Handbook 合订 | 从作者开源仓库取第二版 58 篇，生成 EPUB（61 XHTML）与 PDF（352 页），保留 CC BY-SA 4.0 署名与来源；不随仓库分发 |
| 声明 | NOTICE 增第 7 节「第三方合订本」，写明许可、来源与处理方式；`.gitignore` 排除 `/handbook/`、`/handbook-src/` |

---

## 2026-11-15 v2.1.0（跨模型主打 + 英文 skill + 实测）

| 项 | 变更 |
|---|---|
| 主打能力 | 新增 `skill/references/cross-model-prompts.md`：通用层 K01–K10（模型无关）、适配层 V01–V08（Claude / OpenAI / Gemini 为 A 档；DeepSeek / Qwen / GLM / Kimi / MiniMax 为 B 档，效果较弱需降级）、硬约束 N01–N08、`cross_model_card` schema |
| SKILL.md | description 改为三件事且跨模型列为主打；新增「跨模型」触发类、`target_model` 槽位、`cross_model` 输出档、R22 / R23、§5.4 与 references 索引；version 2.0.0 → 2.1.0 |
| prompt-optimization.md | 新增 O08 与适用判定的转出规则，堵住「指定目标模型却只交付通用优化单」 |
| glossary.md | 增「跨模型提示词优化 / 目标模型 / 通用层 / 模型适配层」4 条术语 |
| 英文 skill | 新增 `skill-en/`（SKILL.md + 10 个 references），211 条规则 ID 与中文版逐条一致；两版二选一安装，README 明示优先英文版 |
| 测试 | 新增 `tests/cross_model_prompt_test.py`（纯标准库、OpenAI 兼容、规则 ID 联动校验）；`.gitignore` 放行 `tests/` 及实测报告 |
| 实测 | 2026-11-15 · `deepseek-flash` · 3 组 A/B：判据首次通过 0/3 → 3/3，累计 token 6,752 → 841（−87.5%），返工 2 轮 → 1 轮；第三组为控制变量（原始版同样要求 JSON 仍失败）；报告 `tests/results/deepseek-flash-20261115.json` |
| README | 中英两版四能力重排：跨模型优化提到第 1 位并成为主打；安装段改为优先英文版；新增「自己跑一次验证」章节；规则数 182 → 211 |
| 中文全译本 | 扉页与「作者介绍」补中文版署名「中文版与更新：Ainstein（ainstein.top）」；重跑 `build_outputs.py` 重建 `dist/` 的 MD / HTML / EPUB（按版权约定不随仓库分发） |

规则统计：211 条 ID 全部唯一，中英两版逐文件一致；本次新增 29 条（K10 + V8 + N8 + R2 + O1）。实测结论只覆盖 3 条样本的单次 A/B，换模型须各自复测。

---

## 后续小批量更新模板

```
## YYYY-MM-DD 小批量更新

| 章节 | 事实 id | 说明 |
|---|---|---|
| NN | model.xxx | 一句话说明 |

同步动作：更新章节末尾 as_of 标记 → 校验行尾 → 重生成成品。
```

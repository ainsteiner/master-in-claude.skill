# Claude 圣经 · 中文工具包

> **把最新 Claude 社区教程、实战秘笈和两本硬核手册，内化成一套 Claude 用户专属 skill。**

[English](README.en.md) ｜ 简体中文

---

## 它是什么

不是又一个提示词合集，而是一套**装进 Claude 就会用的判断规则**。

我们做了三件事：

1. 把 2026 年下半年最新的 **Claude 社区教程、官方 playbook、迁移文档和工程博客**读了一遍；
2. 把两本书逐章读完——**《Claude AI Bible》** 和 **《Claude Code Handbook》第二版**；
3. 去掉重复的、互相冲突的、已经过期的，整理成 **182 条可执行规则**。

装好之后，它能帮你在 Claude 里做四件事：

1. **在 Claude 里把一个需求固化成方案** —— 让强模型想清楚，写成 `SOLUTION.md`；下次换 Sonnet 或 Haiku，照着这份方案也能做到同样的质量。
2. **在 Claude 里改好你手上那条提示词** —— 先诊断 16 项，再重写，逐条告诉你为什么这么改。
3. **决定这件事该交给 Claude 的哪一档** —— 哪个模型、什么 effort、哪个界面，还是做成 Skill / Subagent / MCP / Hook。
4. **让 Claude 在上线前挑毛病** —— 不可逆动作、提示注入、花费上限、哪里会翻车。

---

## 四个能力

### 1 · 在 Claude 里把一个需求固化成方案

这是最划算的一个。**强模型负责想清楚，弱模型负责执行。**

你只要说一句「先出方案，固化下来，后面让 Sonnet 跑」，Claude 会在项目根目录产出 `SOLUTION.md`，固定八节：

| 节 | 内容 | 为什么重要 |
|---|---|---|
| 1 | 目标与成功判据 | 弱模型不知道「算做完」是什么样 |
| 2 | 约束与边界 | 哪些不能动，先说死 |
| 3 | **方案与取舍（含否决项）** | 不写这条，弱模型会把否决过的路再走一遍 |
| 4 | 执行计划 | 每步带「输入 / 动作 / 产物 / 验证判据」 |
| 5 | 关键决策记录 | 附「反悔信号」，什么时候该改路线 |
| 6 | 风险与回退 | 出错怎么退，哪些动作必须人工批 |
| 7 | **交给执行模型的指令块** | 整段可复制，不依赖对话上下文 |
| 8 | 验收清单 | 可跑的检查，不是「仔细看看」 |

一次投入，长期复用。以后换 Sonnet、Haiku 甚至换个人接手，把第 7 节贴过去、按第 4 节一步步走，质量不会塌。

它还会告诉你换模型时怎么补偿：effort 不低于 medium、上下文给全、一次只做一到两步、完成后另起一次干净调用验收。

### 2 · 在 Claude 里改好你手上那条提示词

不是给你一条新的，是先诊断再改。你把原文贴给 Claude，它走五步：

```
复述需求 → 诊断（16 项清单，只报命中的）→ 重写 → 说明每处改动 → 给验证方法
```

> 「你是资深分析师，请对以下行业做深度分析，要求专业、全面、有洞察」

Claude 会指出：没有成功标准、没有输出结构、没有读者定义、没有篇幅与判据；然后重写成带字段、带判据的版本，并给一个能跑的 A/B 验证。原文够好时，它会直接说「不用改」。

输出附**原版对照 + 逐条改动理由 + 效果可能变松还是变紧**，方便你回滚。

### 3 · 决定这件事该交给 Claude 的哪一档

模型、effort、界面，还是该做成 Skill / Subagent / MCP / Hook。它不猜，按顺序问：

> 「客服工单分诊，该用工作流还是智能体？」
> → **工作流**（步骤能写死）。Sonnet 5.5 + medium。先手工处理 200 单，把成功标准写出来。外发前设人工闸门。

> 「团队的周报规范，该写 CLAUDE.md、Skill 还是插件？」
> → **Skill**（一类任务的专长），CLAUDE.md 只留仓库用途与坑，别塞流程。

它还会挡住最常见的过度工程：**看着需要智能体的需求，多数其实是工作流。**

### 4 · 让 Claude 在上线前挑毛病

> 「这套流程有什么风险：agent 读邮件 → 写 CRM → 给客户发确认函」

Claude 会指出：「发确认函」是不可逆外部副作用，必须人工批准；提示注入面在「读邮件」这一步；然后给 deny 规则、沙箱、花费上限，以及一条能跑的验证。

付款、外发、签署、删除这四类动作，它默认永远要人批。

---

## 30 秒装上，马上能用

```bash
git clone https://github.com/ainsteiner/master-in-claude.skill.git
mkdir -p ~/.claude/skills
cp -r master-in-claude.skill/skill ~/.claude/skills/claude-ai-bible-advisor
```

装完不用记命令，直接说话：

> 帮我做一套「每周竞品动态周报」的方案，固化下来，下周我让 Sonnet 照着执行

Claude 会给你一份 `SOLUTION.md`，不是一个临时答案。

注：WorkBuddy 用户把路径换成 `~/.workbuddy/skills/` 即可。

---

## skill 能力信源

先说清楚它不是拍脑袋写的：**规则来自三类材料，逐条对得上出处。**

### 信源一：2026 下半年的 Claude 社区教程与实战秘笈

这部分变化最快，也是最能拉开差距的。我们把下面这些读完、提炼成规则：

- **Claude Code Handbook 第二版的日常更新**（2026-10 核验，全部命令对照官方 CLI 与文档）
- **`claude.dev` 的 15-minute Playbooks**：官方给的起步档、effort 调法、「升一档还是换模型」的成本判断
- **Anthropic 工程博客与官方文档**：上下文工程新规则、Managed Agents 架构、Claude Code best practices、`/doctor prompt-audit`
- **官方迁移指南**：API 2026-10-21 的破坏性变更、Opus 5.5 的 effort 迁移、Fable 5.2 的 toolset 迁移
- **社区实测与踩坑帖**：缓存命中率怎么验、33,000 token 基线、mods 没有沙箱

### 信源二：《Claude AI Bible》

**Stephen Clear ｜ Independent ｜ 2026-06-16**

> 中文版已更新至 2026-11，**[先读全书导读 →](Claude_AI_Bible_00_全书导读.md)**：全书骨架、十条核心判断、按角色的阅读路径。不看书也能先拿到它的判断框架。

一本**操作者手册**，不是 AI 科普。作者是给客户交付过真实系统的人，从 Anthropic 的公司结构讲到生产环境的护栏与账单，几乎不谈论文，只谈「它为什么这样行事」和「你该怎么用它干活」。

它给这套 skill 的东西是**判断框架**：

- 按赌注选模型，不按名头
- 上下文是预算，不是容器
- 能写死就别上智能体
- 关键动作永远留人工闸门
- 160 个实战提示框，每条都有具体代价

### 信源三：《Claude Code Handbook》第二版

**Joey Yu ｜ 2026-10 ｜ CC BY-SA 4.0**

一本**从装到多智能体编排**的完整手册。它不是教程合集，而是被作者按「每章标注核验日期、每章附来源、每个 how-to 附验证方法」重写过一遍的工程文档。

它给这套 skill 的东西是**最新的事实与做法**：

- 斜杠命令并入 Skills 之后，容器到底怎么选（Skill → Subagent → MCP → Hook）
- Auto Mode 默认之后，安全边界从「该不该批准」变成「能够到什么」
- 验证从「读 diff」变成「给 agent 一个能跑出 pass/fail 的检查」
- 上下文五杠杆：缓存稳定前缀、清理工具结果、compaction、按需检索、子智能体隔离

### 我们做了什么

把三类材料对齐之后的差异，写进了正文和规则：

| 维度 | 旧共识（2026 上半年） | 现在 |
|---|---|---|
| 默认档 | Sonnet，按证据往上抬 | **Opus 5.5 + medium effort** 起步，先调 effort 再换模型 |
| 常驻上下文 | 系统提示词一次把重活干了 | **要减规则**，只留读代码看不出来的坑 |
| 验证 | 人工读 diff | **可跑的 pass/fail 检查** + 独立评估器 |
| 容器 | 命令、Skill、MCP、插件并列 | 命令并入 Skills；**决策链** Skill → Subagent → MCP → Hook |
| 界面 | Cowork 是独立入口 | **统一 Claude**，对话可升级为云执行 |

规则全部可溯源：`facts/current.yaml` 里 58 条事实，每条带 `as_of` 与来源等级；书里的判断保留 `book:NN` 章节号。

---

## 我还想看看书怎么获取

两本都能借读，都因为版权原因**不随本仓库分发**，仅限个人学习。

**关注公众号「爱创星球」，按你要的那本回复：**

| 回复 | 你会收到 |
|---|---|
| **「AI圣经」** | 《Claude AI 圣经》中文版（44 章、21.3 万字，EPUB / HTML / Markdown，已更新至 2026-11） |
| **「AI手册」** | 《Claude Code Handbook》第二版（**英文原版，无中文版**；58 篇、352 页，EPUB + PDF） |

说明几点：

- 第一本是中文译本，我们按原书风格更新过——原书出版时 Claude 还是 4 代，现在已是 5 代，29 章重写或替换过。
- 第二本是**英文原版合订，只有英文，没有中文版**。原仓库里的 `zh/` 是第一版的中译且未完成，与第二版章节对不上，所以没有采用。它在 GitHub 上以 CC BY-SA 4.0 开源，合订只是为了方便离线读；作者是 Joey Yu，请也给原仓库点个 Star。
- 两本都请勿商用、勿二次分发。喜欢就支持原版。

---

## 我们是谁

**Ainstein** 在做一件事：一个会陪你长大的私人智能体——**爱创星球**。

不是学习 App，是一个从童年陪你到成年的 agent：记得你、给你建议、陪你做决定，十年后它还在。游戏式 PBL 是它最核心的一项能力，但不是它的全部。

官网：[ainstein.top](https://ainstein.top)　｜　公众号：**爱创星球**

做长期陪伴的 agent，让我们对一件事特别敏感：**模型会换、接口会变、今天的最优解明天就过期。** 所以这个仓库会一直跟着 Claude 更新——这也是我们做这件事的方式。

---

## 安装与目录

```
master-in-claude.skill/
├─ skill/                     ← 装这个
│  ├─ SKILL.md
│  └─ references/
│     ├─ solution-brief.md      方案固化（SOLUTION.md）
│     ├─ prompt-optimization.md 提示词优化（16 诊断 / 12 手法 / 7 约束）
│     ├─ model-selection.md     模型与 effort 选型
│     ├─ routing.md             界面与账号路由
│     ├─ containers.md          容器决策链
│     ├─ agent-design.md        智能体与自动化
│     ├─ guardrails.md          护栏与核实
│     ├─ prompting.md           提示词规则
│     └─ glossary.md            术语对照
├─ facts/current.yaml        事实台账（58 条，带日期与来源）
├─ docs/                     更新规范与资料来源
├─ Claude_AI_Bible_00_全书导读.md  全书骨架 + 十条核心判断 + 阅读路径
├─ CHANGELOG.md
└─ NOTICE.md
```

**Claude Code**（项目级 / 用户级）

```bash
cp -r skill .claude/skills/claude-ai-bible-advisor
cp -r skill ~/.claude/skills/claude-ai-bible-advisor
```

注：WorkBuddy 用户执行 `cp -r skill ~/.workbuddy/skills/claude-ai-bible-advisor`。

---

## 更新节奏

Claude 大约每 6–8 周发一次版本。这个仓库跟着走，方式是克制的：

- 只改事实与受影响段落，**不重排结构**——你的引用不会失效；
- 每次更新在 `CHANGELOG.md` 记一条：日期 / 章节 / 事实 id / 说明；
- 有争议的信息不硬写结论，进 `facts/current.yaml` 的 `conflicts` 段留档。

想第一时间收到：点 **Watch → Custom → Releases**，或直接 **Star**。

---

## 版权

- 《Claude AI Bible》© 2026 Stephen Clear，著作权归作者及出版方所有。**请支持正版。**
- 《Claude Code Handbook》© Joey Yu，采用 **CC BY-SA 4.0** 授权；本仓库不包含其正文，合订本仅通过公众号按个人学习申请提供。
- 本仓库不含原书英文正文，也不含完整中文译本。
- `skill/`、`facts/`、`docs/`、导读、README 为原创部分，采用 **CC BY-NC-SA 4.0**：可署名转载、非商业使用、相同方式共享。
- 文中与台账中的型号、价格、参数均为**核对时点快照**，动手前请以 [Anthropic 官方文档](https://platform.claude.com/docs) 为准。不提供任何担保。
- 权利人如有异议，请开 Issue 或联系我们，核实后立即处理（包括下架）。

---

**如果这套规则帮你省下了半天试错，点个 Star；如果它让你想把那两本书读完，公众号回「AI圣经」或「AI手册」。**

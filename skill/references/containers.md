# 能力容器

## 1. 容器定义

| 容器 | 定义 | 触发范围 |
|---|---|---|
| `prompt` | 一次性指令，不落盘复用 | 单次任务 |
| `skill` | 文件夹 + `SKILL.md`（`name`、`description`、指令正文，可选 `scripts/`、`resources/`）；分层加载 | 某类任务随叫随到，可跨项目 |
| `subagent` | 独立上下文的执行单元，只回传摘要 | 上下文会被读脏，或想换便宜模型跑 |
| `mcp` | 宿主 → MCP 客户端 → MCP 服务器 → 外部系统的标准连接 | 需要 Claude 本来碰不到的外部系统 |
| `plugin` | 把 Skill、MCP 服务器、命令、子智能体一起打包并定版本 | 需要多件一起分发与升级 |
| `mod` | TypeScript 进程内扩展，可改写提示词、拦截工具调用 | 需要进程级改写且能承担无沙箱风险 |
| `claude_md` | 项目根目录常驻上下文：仓库用途、gotchas、硬约束 | 该项目里一直开着 |
| `hook` | 把项目家规编成事件触发的自动动作 | 每次都要完全相同发生的动作 |

- 斜杠命令已并入 Skill：`.claude/commands/x.md` 与 `.claude/skills/x/SKILL.md` 等价，不再单列容器。

## 2. 决策链（按顺序问，命中即停）

| 顺序 | 问 | 结论 |
|---|---|---|
| 1 | 只是流程、规范、清单、方法？ | `skill` |
| 2 | 会读很多文件把主上下文搞脏，或想用便宜模型跑？ | `subagent`（可预载 `skill`） |
| 3 | 需要接 Claude 本来就碰不到的外部系统？ | `mcp` |
| 4 | 必须发生、不管模型怎么想？ | `hook` |
| 5 | 要多件一起分发、定版本？ | `plugin` |

- 默认答案是组合，不是四选一：便宜模型上的 `subagent` + 预载 `skill` + 范围收紧的 `mcp`。
- 判断轴是「东西什么时候进入上下文」：Skill 描述常驻、正文触发才入窗；MCP 只拉用到的工具 schema；Subagent 的阅读发生在你看不见的窗口里，只回传摘要。

## 3. Skill 结构 schema

```yaml
skill:
  name: <kebab-case>
  description: <触发语义 + 不适用范围 + 输出档位判定>
  body: [定义 | 规则 | 流程 | schema | gotchas]
  context: fork        # 可选：在子代理独立上下文里运行
  scripts: []          # 精确确定性操作
  resources: []        # 模板、示例、清单、风格参考
```

- `body` 只允许：定义、规则 / 约束、流程、schema、gotchas。
- 禁止写入设计动机、版本沿革、对话背景、对人类读者的说明性段落。
- 长 skill 拆多文件，正文只留索引与判断；`gotchas` 段是信号最强的部分。

## 4. description 规则

| ID | 规则 | src |
|---|---|---|
| C01 | `description` 是触发扳机：写满用户开口要这类活时真会用的说法，并写明不适用范围 | book:24 |
| C02 | 触发按显式语义匹配，不从执行时段、时间紧迫、请求长短推断模板类型 | convention |
| C03 | 轻量档命名去掉时段前缀，避免把「某时段执行」误匹配成轻量模板 | convention |
| C04 | `description` 常驻加载；正文触发才加载；脚本与资源被点名才加载 | book:24 |
| C05 | Skill 是软件：写完后用真实样本测试并迭代，不假设首版正确 | book:24 |
| C06 | 正文在真在意的地方写具体，在不在意的地方留余地 | book:24 |
| C07 | 一库 Skill 需去重与划分边界，重叠触发判为待修问题 | book:24 |

## 5. 容器边界规则

| ID | 规则 | src |
|---|---|---|
| C08 | 任务反复 + 有定见才做 `skill`；一次性问题直接问 | book:24 |
| C09 | 要接外部系统用 `mcp`；`skill` 不替代连接，`mcp` 也不当文档加载器 | book:24,26 |
| C10 | 容器按 §2 决策链取，默认组合而非四选一 | cur:cc.commands_merged |
| C11 | `mcp` 架构固定为宿主 / 客户端 / 服务器三层；连接 = 告诉宿主如何启动服务器与需要什么凭据 | book:26 |
| C12 | 服务器按类别识别而非列具体产品；具体可用性从事实台账取 | book:26 |
| C13 | 插件与 mod 安装前审查来源、权限与更新策略；团队需治理与版本约定 | book:25,cur:cc.mods |
| C14 | 项目文件管一个工作区的规矩，Skill 管一类任务的专长；把任务专用方法塞进项目文件判为错放 | book:24 |
| C15 | `claude_md` 轻量：写仓库用途与读代码看不出来的 gotchas，目标 200 行以内 | cur:prompt.claude_md |
| C16 | Claude 违反约定时把规则加进 `claude_md` 或用 `hook` 固化，不在会话里反复训斥 | book:23 |
| C17 | 子智能体用于上下文卫生与模型杠杆：各自独立上下文，只回传摘要 | book:23,cur:prompt.then_now |
| C19 | MCP 工具多时启用延迟加载（ToolSearch）：只载入工具名，用到才取 schema | cur:agent.tool_search |
| C20 | 成熟 MCP 流程可降级封装为 Skill：token 从万级降到百级，因为正文只在触发时加载 | cur:agent.tool_search |
| C21 | Skill 已成跨 Claude Code / Agent SDK / Skills API 的开放标准，可移植 | cur:prompt.skill_style |
| C22 | 需要在独立上下文运行时在 frontmatter 写 `context: fork`；agent 可用 `skills:` 预载技能 | cur:prompt.skill_style |
| C23 | `mod` 无沙箱，以用户权限在进程内运行；只装可信来源、锁版本，团队先加载内置安全 mod | cur:cc.mods |

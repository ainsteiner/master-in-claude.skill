# Capability Containers

## 1. Container definitions

| Container | Definition | Trigger scope |
|---|---|---|
| `prompt` | One-off instruction, not persisted for reuse | Single task |
| `skill` | Folder + `SKILL.md` (`name`, `description`, instruction body; optional `scripts/`, `resources/`); loaded in layers | Expertise for a class of task, on call, portable across projects |
| `subagent` | Execution unit with its own context, returns only a summary | Reading would dirty the main context, or a cheaper model should run it |
| `mcp` | Standard connection: host → MCP client → MCP server → external system | Needs an external system Claude cannot otherwise reach |
| `plugin` | Bundles Skills, MCP servers, commands and subagents with a version | Several pieces must be distributed and upgraded together |
| `mod` | In-process TypeScript extension that can rewrite prompts and intercept tool calls | Process-level rewriting is needed and the no-sandbox risk is acceptable |
| `claude_md` | Always-on context at project root: repo purpose, gotchas, hard constraints | Always on inside that project |
| `hook` | Encodes house rules as event-triggered automatic actions | An action that must happen identically every time |

- Slash commands have merged into Skills: `.claude/commands/x.md` and `.claude/skills/x/SKILL.md` are equivalent; commands are no longer a separate container.

## 2. Decision chain (ask in order, stop on first hit)

| Order | Question | Conclusion |
|---|---|---|
| 1 | Just a process, standard, checklist or method? | `skill` |
| 2 | Reads many files and dirties the main context, or should run on a cheaper model? | `subagent` (can preload a `skill`) |
| 3 | Needs an external system Claude cannot otherwise reach? | `mcp` |
| 4 | Must happen regardless of what the model decides? | `hook` |
| 5 | Several pieces must ship and version together? | `plugin` |

- The default answer is a combination, not a four-way pick: a `subagent` on a cheaper model + a preloaded `skill` + a scope-tightened `mcp`.
- The deciding axis is "when does the thing enter context": a Skill description is always on, its body enters only on trigger; MCP pulls only the tool schemas used; a Subagent's reading happens in a window you never see and returns only a summary.

## 3. Skill structure schema

```yaml
skill:
  name: <kebab-case>
  description: <trigger semantics + exclusions + output-tier rules>
  body: [definition | rule | flow | schema | gotchas]
  context: fork        # optional: run in a subagent's own context
  scripts: []          # precise deterministic operations
  resources: []        # templates, examples, checklists, style references
```

- `body` allows only: definitions, rules / constraints, flows, schemas, gotchas.
- Design rationale, version history, conversational background and explanatory paragraphs for human readers are forbidden.
- Split a long skill into multiple files; the body keeps only an index and judgment; the `gotchas` section carries the strongest signal.

## 4. Description rules

| ID | Rule | src |
|---|---|---|
| C01 | `description` is the trigger: write the phrases a user actually says when asking for this work, and state what it does not cover | book:24 |
| C02 | Trigger by explicit semantic match; never infer a template from execution window, time pressure or request length | convention |
| C03 | Lightweight-tier names drop time-of-day prefixes so "run before open" is not misread as a lightweight template | convention |
| C04 | `description` is always loaded; the body loads on trigger; scripts and resources load only when named | book:24 |
| C05 | A Skill is software: test it with real samples and iterate; never assume v1 is correct | book:24 |
| C06 | Be specific where it matters and leave room where it does not | book:24 |
| C07 | A library of Skills needs deduplication and boundaries; overlapping triggers are a defect to fix | book:24 |

## 5. Container boundary rules

| ID | Rule | src |
|---|---|---|
| C08 | Recurring task + a settled view → `skill`; a one-off question is just asked | book:24 |
| C09 | External systems use `mcp`; a `skill` is not a substitute for a connection, and `mcp` is not a document loader | book:24,26 |
| C10 | Take the container from the §2 decision chain; default to a combination, not a four-way pick | cur:cc.commands_merged |
| C11 | MCP architecture is fixed at three layers: host / client / server; a connection tells the host how to start the server and what credentials it needs | book:26 |
| C12 | Identify servers by category, not by listing concrete products; availability comes from the fact ledger | book:26 |
| C13 | Review the source, permissions and update policy before installing plugins and mods; teams need governance and version conventions | book:25,cur:cc.mods |
| C14 | Project files govern one workspace's rules, Skills govern expertise for a class of task; putting task-specific methods in a project file is misplacement | book:24 |
| C15 | `claude_md` stays light: repo purpose and gotchas invisible in code, target under 200 lines | cur:prompt.claude_md |
| C16 | When Claude breaks a convention, add it to `claude_md` or fix it with a `hook`; do not scold it repeatedly in chat | book:23 |
| C17 | Subagents serve context hygiene and model leverage: each has its own context and returns only a summary | book:23,cur:prompt.then_now |
| C19 | With many MCP tools, enable deferred loading (ToolSearch): load tool names only, fetch a schema when used | cur:agent.tool_search |
| C20 | A mature MCP flow can be downgraded into a Skill: tokens drop from tens of thousands to hundreds because the body loads only on trigger | cur:agent.tool_search |
| C21 | Skills are an open standard across Claude Code / Agent SDK / Skills API and are portable | cur:prompt.skill_style |
| C22 | For an isolated context write `context: fork` in frontmatter; agents can preload skills via `skills:` | cur:prompt.skill_style |
| C23 | A `mod` has no sandbox and runs in-process with the user's permissions; install only trusted sources, pin versions, and load built-in safety mods first in teams | cur:cc.mods |

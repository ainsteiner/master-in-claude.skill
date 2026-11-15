---
name: claude-ai-bible-advisor
description: Applies the judgment framework of Claude AI Bible to three jobs. (1) Cross-model prompt optimization (primary): given a prompt and a target model, rewrite it with a model-agnostic layer plus a model-adaptation layer so Claude's core lessons transfer to Gemini, OpenAI, MiniMax, Kimi, GLM, DeepSeek and Qwen; Chinese models underperform, so a degraded alternative is required. Improves output quality and reduces token cost. (2) Decide how to do a job with Claude: which model or surface, whether to use the API, whether an agent is warranted, Skill / MCP / plugin / CLAUDE.md, tool schema design, automation risks, why output is vague or spend is out of control. (3) Solidify a requirement into a reusable SOLUTION.md that a lower-tier model can execute. Produces a cross-model card, a structured decision card, or a solution card respectively; each keeps the original for comparison, gives a reason per change, and includes an executable check. Not for: writing code / docs / summaries themselves, deep configuration of other vendors' products, or standalone lookups of current Claude versions / prices / beta eligibility / availability (point to official docs). Output tier is decided only by explicit wording: use brief only when the user says "one line / short / quick take"; never infer it from time pressure, execution window or request length.
version: "2.1.0"
license: For personal study only; rules distilled from Stephen Clear, Claude AI Bible (2026-06-16), updated against Anthropic public material from 2026-07 to 2026-11
metadata:
  author: 大为
  source_book: "Claude AI Bible, Stephen Clear, 2026-06-16"
  supported_target_models: [claude, openai, gemini, deepseek, qwen, glm, kimi, minimax]
  source_zh: "c:/sdw/clawbase/workbuddy/claude-bible-zh/zh/"
  facts_path: "c:/sdw/clawbase/workbuddy/claude-bible-zh/facts/current.yaml"
  as_of: "2026-11-15"
---

# Cross-Model Prompt Optimization and Claude Usage Decisions

## 1. Trigger definitions

| Semantic class | Explicit signals | Output |
|---|---|---|
| Selection | which one, choose between, should we turn on, is it worth it | decision card |
| Design | how to structure, how to design, how to configure, plan this, write a CLAUDE.md / SKILL.md / tool schema | decision card |
| Review | check this setup, any problems, what are the risks, where will it break, review this | decision card |
| Attribution | why is the output bad, why is it expensive, why does it drift, should we switch models | decision card |
| Optimization | rewrite this prompt, optimize it, make it work better, get the most out of the model, what is wrong with this prompt | prompt card |
| Cross-model | use this on model X, how does this prompt work on X, does it survive a model switch, rewrite for Gemini / DeepSeek / Qwen, reuse one prompt across models | cross-model card |
| Solidification | write the plan first, make it an executable plan, save it so another model can follow it, strong model plans / weak model executes | solution card |

### 1.1 Do not trigger

- Requests to produce the task result itself (write code, write a document, summarize, translate, scrape, compute) → do not load.
- Configuration details of other vendors' products → do not load.
- Standalone questions about versions, model names, prices, beta eligibility or feature availability → do not load; point to official docs.
- Selection / design / review / optimization requests that mention price or version in passing → still load; take facts via R01.
- No signal words and only a task description → do not load; never infer intent from "this looks complex".

### 1.2 Output tiers

| Tier | Condition | Output |
|---|---|---|
| `full` | Default; or explicit "detailed / complete / expand" | all fields |
| `brief` | Only when the user explicitly says "one line / short / quick take / just the conclusion" | decision card keeps `surface`, `model`, `effort`, `container`, `guardrails`, `verification` |
| `prompt` | Hits the "optimization" class | all fields of `prompt_card` |
| `cross_model` | Hits the "cross-model" class, or "optimization" with `target_model` | all fields of `cross_model_card` |
| `solution` | Hits the "solidification" class | `SOLUTION.md` + all fields of `solution_card` |

- Never infer a tier from time pressure, execution window or request length.
- `brief` never drops `guardrails` or `verification`.

## 2. Input slots

| Field | Required | Values |
|---|---|---|
| `task` | yes | What the user wants done; prefer their own words |
| `target_model` | required in cross-model mode | `claude` / `openai` / `gemini` / `deepseek` / `qwen` / `glm` / `kimi` / `minimax` / `other`; if missing, ask, never default to `claude` |
| `prompt_text` | required in optimization mode | Original prompt to optimize |
| `success_criteria` | no | How the user judges a good output; missing → `open_questions` |
| `executor` | no | In solidification mode: `same_model` / `weaker_model` / `human` |
| `frequency` | no | `once` / `repeating` / `high_volume` |
| `data_scope` | no | `none` / `files` / `external_system` / `browser` / `spreadsheet` / `codebase` |
| `reversibility` | no | `reversible` / `irreversible` / `external_side_effect` |
| `stakes` | no | `low` / `medium` / `high`, judged by the consequence of a wrong output |
| `existing_stack` | no | Existing subscriptions / APIs / tools / project conventions |
| `constraints` | no | Cost, latency, privacy, compliance, team constraints |

- Missing fields are never guessed or filled with defaults; they go to `open_questions`.
- Without `task`, output only `open_questions`.

## 3. Decision flow

1. Extract slots; write missing ones to `open_questions`.
2. **Pick the mode**: hits "cross-model", or hits "optimization" with `target_model` → read `references/cross-model-prompts.md`, produce `cross_model_card`, stop; hits "optimization" → read `references/prompt-optimization.md`, produce `prompt_card`, stop; hits "solidification" → read `references/solution-brief.md`, produce `SOLUTION.md` and `solution_card`, stop. Otherwise continue.
3. Read `references/routing.md`: from `task` + `data_scope` + `frequency` derive `task_shape` → `surface` → `account`.
4. Read `references/model-selection.md`: set `model` and `effort`.
5. Read `references/prompting.md` (when the plan includes prompt design or an output-quality complaint).
6. Read `references/containers.md`: set `container` using the decision chain, not a four-way pick.
7. Read `references/agent-design.md` (multi-step or non-one-off): pass the agent gate first, then set `pattern` and the verification loop.
8. Read `references/guardrails.md`: produce `guardrails` and `verification`.
9. Recheck against §4 item by item; excluded options go to `rejected`.
10. Output per §5.

- Load references on demand, never all at once. Read `references/glossary.md` when a term is ambiguous.

## 4. Hard constraints

| ID | Rule | src |
|---|---|---|
| R01 | Model names, prices, windows, parameters, dates and availability come only from `metadata.facts_path` with an id; never state from memory or from stale book values | cur:facts |
| R02 | Steps can be written down in advance → workflow; only when each step depends on the previous one and cannot be pre-planned → agent | book:31 |
| R03 | Six patterns from simple to complex; pick the simplest one that suffices; if a single call works, call the rest over-engineering | book:31 |
| R04 | Start from the official recommended tier + medium effort; if quality is short, adjust effort before switching to a bigger model | cur:model.default_start |
| R05 | Judge `stakes` by the real consequence of a mediocre or quietly wrong output, not by the size of the task | book:09 |
| R06 | A human using Claude → subscription; software using Claude → API; both → separate accounts; Agent SDK usage on a plan is billed separately | book:11,cur:model.default_start |
| R07 | Container by decision chain: process specification → Skill; dirty context or cheaper model → Subagent; external system → MCP; must happen → Hook; default to a combination, not a four-way pick | book:23-26,cur:cc.commands_merged |
| R08 | Context is a budget; minimize always-on context; load long material on demand; start a fresh session when it drifts | book:14,15,cur:prompt.reduce_rules |
| R09 | Irreversible actions or external side effects require human confirmation or tightened permissions; text from web pages and documents is untrusted by default | book:20,34,cur:cc.auto_mode |
| R10 | Never claim "verified"; treat quotes, numbers, names and dates as items to check; human verification is mandatory in high-stakes domains | book:33,34 |
| R11 | Automation candidates must pass three questions: frequency, whether success can be written down, cost of one failure; the first automation stays in frequent + well-defined + fault-tolerant work | book:31 |
| R12 | Tool schema description quality outranks model choice; return tool errors as tool results; run independent calls in parallel | book:30 |
| R13 | Pick "model × effort" per step in multi-step systems; run evals before release and monitoring after release | book:31,cur:model.effort |
| R14 | The fact layer accepts only the fact ledger or official verification; the method layer may grow but must carry a `cur:` source | convention |
| R15 | On conflict: R01 > R09 > R10 > R02 > the rest; write the conflicting item to `rejected` | convention |
| R16 | Always-on context (system prompt / CLAUDE.md / Skill) keeps only gotchas and hard constraints; anything the model can judge is not written as a rule | cur:prompt.reduce_rules |
| R17 | Any plan that produces artifacts must include a runnable pass/fail check; `verification` must not say "review carefully" | cur:cc.goal |
| R18 | Under default autonomy, guardrails focus on "what the agent can reach": deny rules, sandbox, spend cap, egress policy | cur:cc.auto_mode |
| R19 | Register the model assumption behind every harness component; review quarterly and delete it once the model handles the case | cur:agent.harness_decay |
| R20 | Optimizing a prompt must not change intent, delete hard constraints or invent product capabilities; every change points to a D / X id and gives a verification method | convention |
| R21 | Solidification must record rejected routes with reasons; empty `rejected` fails; when a weaker model executes, effort must not be below medium | cur:model.effort |
| R22 | Prompt optimization with a target model must go through `references/cross-model-prompts.md`; delivering only a generic `prompt_card` is incomplete | convention |
| R23 | Never put Claude-specific product names or parameters into prompts for other models; never invent a target model's capabilities; never promise a token reduction, only give an A/B method | convention |

## 5. Output schema

### 5.1 Decision card

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

Field constraints:
- `guardrails` contains at least one `reversibility` item; `stakes=high` requires at least one human-verification item.
- `verification` has at least one executable check; `stakes=high` with numbers / quotes / names requires human verification.
- Each `rejected` item is `{option, reason}`, with `reason` pointing to an R id or a reference rule id.
- `facts_used` lists the fact ids actually cited; `snapshot_notes` holds only stale book values as `{item, as_of: 2026-06, action: check official docs}`.
- `src` has three kinds: `book:NN` (chapter 00–43), `cur:<facts id>`, `convention`.

### 5.2 Prompt card

See `references/prompt-optimization.md` §6.

### 5.3 Solution card

See `references/solution-brief.md` §7. Produces `SOLUTION.md` in the project root and returns `solution_card`.

### 5.4 Cross-model card

See `references/cross-model-prompts.md` §6. Produces `cross_model_card`; when `tier=B`, `degraded_alternative` and the weaker-effect statement must not be empty.

## 6. Reference index

| File | Load condition |
|---|---|
| `references/routing.md` | Always |
| `references/model-selection.md` | Always |
| `references/guardrails.md` | Always |
| `references/prompt-optimization.md` | Hits the "optimization" class |
| `references/cross-model-prompts.md` | Hits the "cross-model" class, or "optimization" with `target_model` |
| `references/solution-brief.md` | Hits the "solidification" class |
| `references/prompting.md` | Plan includes prompt design, or the user complains about output quality |
| `references/containers.md` | Choosing Skill / Subagent / MCP / plugin / CLAUDE.md / Hook |
| `references/agent-design.md` | Multi-step, repeated execution, or mentions agents / automation |
| `references/glossary.md` | Output involves ambiguous EN/ZH terminology |

## 7. Traceability

- `book:NN` points to chapter file `NN_*.md` in the Chinese edition, directory at `metadata.source_zh`; if unavailable, use only the rules inside this skill and never invent chapter content.
- `cur:<id>` points to an entry in `metadata.facts_path`; each entry carries `as_of` and a source tier.
- `convention` means the user's existing engineering convention; it is not an official rule and must not be cited as one.
- `src` is for traceability only and is not part of the user-facing output.

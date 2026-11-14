# The Claude Playbook · Toolkit

> **The latest Claude community guides, field-tested tips, and two serious handbooks — distilled into one installable skill for Claude users.**

English ｜ [简体中文](README.md)

---

## What this is

Not another prompt collection. It is a set of **judgment rules that Claude actually uses once installed**.

We did three things:

1. Read through the **latest Claude community guides, official playbooks, migration notes and engineering posts** from the second half of 2026;
2. Read two books chapter by chapter — **Claude AI Bible** and **Claude Code Handbook, 2nd edition**;
3. Dropped what was duplicated, contradictory or already outdated, and turned the rest into **182 executable rules**.

Once installed, it helps you do four things inside Claude:

1. **Turn a requirement into a reusable plan, inside Claude** — the strong model thinks it through and writes `SOLUTION.md`; next time Sonnet or Haiku can execute at the same quality.
2. **Fix the prompt you already have, inside Claude** — 16 diagnostics first, then a rewrite with a reason for every change.
3. **Decide which layer of Claude this belongs to** — which model, what effort, which surface, or whether it should be a Skill / Subagent / MCP / Hook.
4. **Have Claude poke holes before you ship** — irreversible actions, prompt injection, spend caps, where it breaks.

---

## The four capabilities

### 1 · Turn a requirement into a reusable plan, inside Claude

The highest-leverage one. **The strong model thinks; the cheaper model executes.**

Say "write the plan first, save it, Sonnet will run it next week" and Claude produces `SOLUTION.md` in your project root, with eight fixed sections:

| § | Content | Why it matters |
|---|---|---|
| 1 | Goal and success criteria | The weaker model has no idea what "done" means |
| 2 | Constraints and boundaries | What must not change, stated up front |
| 3 | **Approach and trade-offs, including rejected options** | Without this, the weaker model walks the rejected path again |
| 4 | Execution plan | Each step: input / action / artifact / check |
| 5 | Decision log | With "regret signals" — when to change course |
| 6 | Risks and rollback | How to back out; which actions need a human |
| 7 | **Handoff prompt for the executor** | One pasteable block, no conversation context needed |
| 8 | Acceptance checklist | Runnable checks, not "review carefully" |

One investment, reused indefinitely. Swap in Sonnet, Haiku, or a different person: paste §7, follow §4, quality holds.

It also tells you how to compensate when downgrading: effort no lower than `medium`, give the full context, one or two steps at a time, and verify with a fresh call afterwards.

### 2 · Fix the prompt you already have, inside Claude

It does not hand you a new prompt. It diagnoses first. Paste the original, and Claude runs five steps:

```
Restate intent → Diagnose (16 checks, report only hits) → Rewrite → Explain each change → Give a check
```

> "You are a senior analyst. Analyse the following industry in depth. Be professional, comprehensive and insightful."

Claude points out: no success criteria, no output structure, no defined reader, no length or acceptance test; then rewrites it with fields and checks, plus a runnable A/B verification. If the original is already good, it says "no change needed".

Output includes **the original side by side, the reason for each edit, and whether the result got looser or tighter** — so you can roll back.

### 3 · Decide which layer of Claude this belongs to

Model, effort, surface — or whether it should be a Skill / Subagent / MCP / Hook. It does not guess; it asks in order:

> "Support ticket triage — workflow or agent?"
> → **Workflow** (steps can be written down). Sonnet 5.5 + medium. Handle 200 tickets by hand first and write down the success criteria. Human gate before anything goes out.

> "Our weekly report standard — CLAUDE.md, Skill or plugin?"
> → **Skill** (expertise for a class of task). Keep CLAUDE.md to repo purpose and gotchas; don't stuff process into it.

It also blocks the most common over-engineering: **most requirements that look like they need an agent are actually workflows.**

### 4 · Have Claude poke holes before you ship

> "What's risky here: agent reads email → writes CRM → sends confirmation to the customer"

Claude flags: "send confirmation" is an irreversible external side effect and needs human approval; the injection surface is "reads email"; then it gives deny rules, sandboxing, a spend cap, and a runnable check.

Payment, outbound messages, signing and deletion always require a human by default.

---

## Install in 30 seconds

```bash
git clone https://github.com/ainsteiner/master-in-claude.skill.git
mkdir -p ~/.claude/skills
cp -r master-in-claude.skill/skill ~/.claude/skills/claude-ai-bible-advisor
```

Then just talk:

> Turn my weekly competitor digest into a plan, save it, and let Sonnet execute it next week.

You get a `SOLUTION.md`, not a one-off answer.

Note: WorkBuddy users can use `~/.workbuddy/skills/` instead.

---

## Where the rules come from

None of this is improvised. **Every rule traces back to one of three sources.**

### Source 1 · Claude community guides and field notes, second half of 2026

This is the fastest-moving part, and where the edge comes from. We read and distilled:

- **Ongoing updates to Claude Code Handbook, 2nd edition** (verified 2026-10; every command checked against the official CLI and docs)
- **`claude.dev` 15-minute Playbooks**: the recommended starting tier, how to dial effort, and how to compare "one more effort level" against "a bigger model"
- **Anthropic engineering posts and docs**: the new context-engineering rules, Managed Agents architecture, Claude Code best practices, `/doctor prompt-audit`
- **Official migration guides**: API 2026-10-21 breaking changes, the Opus 5.5 effort migration, the Fable 5.2 toolset migration
- **Community measurements and post-mortems**: how to verify cache hits, the ~33,000-token baseline, mods having no sandbox

### Source 2 · Claude AI Bible

**Stephen Clear ｜ Independent ｜ 2026-06-16**

> Chinese edition updated to 2026-11. **[Start with the reading guide →](Claude_AI_Bible_00_全书导读.md)** — book structure, ten core takeaways, and reading paths by role. You can take its judgment framework without reading the whole book first.

An **operator's manual**, not an AI primer. The author has shipped real systems for clients; the book runs from Anthropic's corporate structure to production guardrails and billing, with almost no papers and a lot of "why it behaves this way" and "how to make it do your work".

What it contributes is the **judgment framework**:

- Pick models by stakes, not by prestige
- Context is a budget, not a container
- If you can write the steps down, don't use an agent
- Irreversible actions always keep a human gate
- 160 field-tested callouts, each with a concrete cost attached

### Source 3 · Claude Code Handbook, 2nd edition

**Joey Yu ｜ 2026-10 ｜ CC BY-SA 4.0**

A complete path **from first install to multi-agent orchestration**. Not a tutorial dump — the author rebuilt it around "each chapter states when it was verified, each chapter ends with dated sources, each how-to ends with a way to check it worked".

What it contributes is the **current facts and practice**:

- After slash commands merged into Skills, how to choose a container (Skill → Subagent → MCP → Hook)
- After Auto Mode became the default, security shifted from "should I approve this" to "what can it reach"
- Verification moved from "read the diff" to "give the agent a check that returns pass/fail"
- The five context levers: cached stable prefix, clearing tool results, compaction, retrieval on demand, subagent isolation

### What we changed

Where the three sources disagreed, we picked the current answer and wrote it into both the book and the rules:

| Dimension | Old consensus (H1 2026) | Now |
|---|---|---|
| Default tier | Sonnet, escalate on evidence | Start on **Opus 5.5 + medium effort**; adjust effort before switching models |
| Always-on context | System prompt carries the weight | **Cut rules**; keep only gotchas and hard constraints |
| Verification | Humans read diffs | **A runnable pass/fail check** plus an independent evaluator |
| Containers | Commands, Skills, MCP, plugins side by side | Commands merged into Skills; **decision chain** Skill → Subagent → MCP → Hook |
| Surfaces | Cowork is a separate entry | **Unified Claude**; a chat can escalate to cloud execution |

Everything is traceable: `facts/current.yaml` holds 58 facts, each with `as_of` and a source tier; book-derived rules keep a `book:NN` chapter reference.

---

## How to get the two books

Both are available to borrow for personal study. Neither ships with this repository, for copyright reasons.

**Follow the WeChat account 「爱创星球」 and reply with the keyword:**

| Reply | You get |
|---|---|
| **「AI圣经」** | *Claude AI Bible*, Chinese edition (44 chapters, ~213k characters, EPUB / HTML / Markdown, updated to 2026-11) |
| **「AI手册」** | *Claude Code Handbook*, 2nd edition (**English only — no Chinese edition**; 58 chapters, 352 pages, EPUB + PDF) |

A few notes:

- The first is a Chinese translation we updated in the author's own style — the original was written when Claude was still generation 4; 29 chapters were rewritten or revised.
- The second is the **English original, English only**. The repo's `zh/` folder is an unfinished translation of the *first* edition and does not match the second edition's chapters, so it was not used. It is open source on GitHub under CC BY-SA 4.0; the author is Joey Yu — please star the original repo too.
- Neither may be used commercially or redistributed. If you like them, support the originals.

---

## Who we are

**Ainstein** is building one thing: a private agent that grows up with you — **Ainstein Planet (爱创星球)**.

Not a study app. An agent that stays from childhood into adulthood: it remembers you, advises you, decides with you, and is still there ten years later. Game-based PBL is its core capability, but not the whole product.

Website: [ainstein.top](https://ainstein.top)　｜　WeChat: **爱创星球**

Building a long-term companion agent makes us sensitive to one thing: **models change, APIs change, today's optimum expires tomorrow.** So this repository keeps tracking Claude — that is simply how we work.

---

## Install and layout

```
master-in-claude.skill/
├─ skill/                     ← install this
│  ├─ SKILL.md
│  └─ references/
│     ├─ solution-brief.md      plan solidification (SOLUTION.md)
│     ├─ prompt-optimization.md prompt optimization (16 diagnostics / 12 techniques / 7 constraints)
│     ├─ model-selection.md     model and effort selection
│     ├─ routing.md             surface and account routing
│     ├─ containers.md          container decision chain
│     ├─ agent-design.md        agents and automation
│     ├─ guardrails.md          guardrails and verification
│     ├─ prompting.md           prompting rules
│     └─ glossary.md            terminology
├─ facts/current.yaml        58 facts, each dated and sourced
├─ docs/                      update conventions and sources
├─ Claude_AI_Bible_00_全书导读.md  Chinese reading guide (structure + ten takeaways)
├─ CHANGELOG.md
└─ NOTICE.md
```

**Claude Code** (project-level / user-level)

```bash
cp -r skill .claude/skills/claude-ai-bible-advisor
cp -r skill ~/.claude/skills/claude-ai-bible-advisor
```

Note: WorkBuddy users run `cp -r skill ~/.workbuddy/skills/claude-ai-bible-advisor`.

---

## Update cadence

Claude ships roughly every 6–8 weeks. This repo follows, conservatively:

- Only facts and affected passages change; **structure never moves**, so your links and notes stay valid;
- Every update gets one line in `CHANGELOG.md`: date / chapter / fact id / note;
- Disputed facts are not forced into a conclusion — they land in the `conflicts` section of `facts/current.yaml`.

To get updates: **Watch → Custom → Releases**, or just **Star**.

---

## License

- *Claude AI Bible* © 2026 Stephen Clear. All rights reserved by the author and publisher. **Please buy the original.**
- *Claude Code Handbook* © Joey Yu, licensed **CC BY-SA 4.0**. This repository does not contain its text; the bundled edition is available on request for personal study.
- This repository contains neither the original English text nor the full Chinese translation.
- Original parts (`skill/`, `facts/`, `docs/`, the reading guide, this README) are licensed **CC BY-NC-SA 4.0**: share with attribution, non-commercial, same license.
- All model names, prices and parameters are **snapshots at verification time**. Check the [official Anthropic docs](https://platform.claude.com/docs) before acting. No warranty.
- Rights holders: open an issue or contact us; verified requests are handled immediately, including takedown.

---

**If these rules saved you half a day of trial and error, star the repo. If they made you want to read the two books, reply 「AI圣经」 or 「AI手册」 on WeChat.**

# Model and Effort Selection

## 1. Tier definitions

| Tier | For | Not for |
|---|---|---|
| `fable` | Hardest open-ended technical problems, large cross-repo changes, long unattended runs, when high-tier Opus is not enough | Everyday coding and knowledge work |
| `opus` | Default starting tier; complex long-running agents, large refactors, deep research | Simple classification, extraction, routing |
| `sonnet` | Well-scoped everyday work, building to a spec, fixing bugs | Vague goals, work needing ongoing judgment |
| `haiku` | Classification, routing, extraction, subtasks, high throughput | Deep reasoning, careful number checking |

## 2. Effort tiers

| Tier | For |
|---|---|
| `low` | Simple lookups, format conversion, rule-based dispatch |
| `medium` | Default starting tier; most coding and knowledge work |
| `high` | Hard problems, multi-step reasoning, self-correction |
| `xhigh` | Extremely hard, near the capability ceiling |
| `max` | Only when confirmed worth it; cost and latency rise sharply |

- The fixed `thinking: enabled / disabled` form is rejected by the API; the choice is now a tier, not a budget.
- Above `high`, compare "one tier higher" with "a bigger model" before defaulting to an upgrade.

## 3. Decision flow

1. Judge reasoning difficulty and `stakes`; set `stakes` by consequence, not by task size.
2. Start from the official recommended tier + `medium` effort.
3. Well-scoped and self-checkable → `sonnet`; high-throughput classification / extraction → `haiku`; genuinely hardest and high-tier Opus still short → `fable`.
4. If output falls short: raise effort first; if `high` is still short, move up a model tier.
5. Repeat 2–4 per step in multi-step systems; do not set one tier for the whole system.
6. Write the conclusion to `model` and `effort`; rejected tiers go to `rejected`.

## 4. Rules

| ID | Rule | src |
|---|---|---|
| M01 | Start from the official recommended tier + `medium` effort; pick by the total cost of finishing one job, not by unit price | cur:model.default_start |
| M02 | Judge `stakes` by the consequence of a wrong answer, not by how important, visible or glamorous the task feels | book:09 |
| M03 | Match effort to difficulty; simple lookups and high-throughput classification use a low tier, not a high one | cur:model.effort |
| M04 | Context is a budget; `1M` is a ceiling, not a target; load long material on demand, never dump the whole stack | cur:model.context_window |
| M05 | Model names, prices, windows and retirement dates come from the fact ledger, never written into rules or body text | cur:facts |
| M06 | Stable prefixes use caching; at most 4 breakpoints per request, byte-identical prefix, verify hits with `cache_read_input_tokens` | cur:price.cache_breakpoints |
| M07 | Speed and cost are selection axes alongside quality; write latency constraints explicitly into `constraints` | book:09,31 |
| M08 | Leaderboards and third-party reviews are a coarse filter only; scores from different index versions are not comparable; the deciding evidence is an A/B on your own real task | book:09,10 |
| M09 | Do not hard-code a model name deep in the system; keep a replaceable configuration for retirements | book:09 |
| M10 | Do not exclude other models out of brand loyalty or single-model preference; assign a multi-model workflow by task | book:10 |
| M11 | Never assume a prompt ports unchanged across models; re-verify on real tasks after a switch | book:10 |
| M12 | Per-step "model × effort" is a required output for `multi_step_unpredictable` | book:31 |
| M13 | `stakes=high` with numbers, quotes or names requires human verification in `verification` | book:33,34 |
| M14 | If output falls short, adjust effort first, then switch models; this is the default order, do not skip | cur:prompt.effort_first |
| M15 | Above `high`, compare the measured cost of "one tier up" against "a bigger model"; do not guess | cur:prompt.effort_first |
| M16 | Check retirement dates when selecting; retired or near-retirement models must not enter a new plan | cur:model.retirements |
| M17 | `fable` can act as Advisor (consulted by smaller models) or Orchestrator (dispatching to them); it need not execute every step | cur:agent.advisor |
| M18 | Cache writes carry a premium and pay back only on the second hit; do not set a long TTL on a one-off prefix | cur:price.cache_ttl |

## 5. Output mapping

| Condition | `model` | `effort` |
|---|---|---|
| Default start (complex coding / knowledge work) | `opus` | `medium` |
| Well-scoped, self-checkable | `sonnet` | `medium` or `high` |
| Simple + high throughput / latency first | `haiku` | `low` or `medium` |
| Hardest + `stakes=high` + long unattended run | `fable` | `high` or above |
| Multi-step system | `per_step` | `per_step` |
| Insufficient evidence | `unknown` | `unknown`, add a real-task A/B suggestion to `open_questions` |

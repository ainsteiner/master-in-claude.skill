# Plan Solidification (SOLUTION.md)

## 1. Applicability

| Hits | Does not hit |
|---|---|
| Write the plan first / plan before doing | Asking for finished code, copy or a document |
| Turn this requirement into an executable plan | Asking how one API works |
| Save it as a document so another model can follow it later | One-off Q&A |
| Strong model plans, weak model executes | — |

Core use: **freeze a strong model's planning quality into a file so a lower-tier model can execute the same approach**. One investment, reused indefinitely.

## 2. Input slots

| Field | Required | Meaning |
|---|---|---|
| `need` | yes | The user's original requirement; prefer their own words |
| `success_criteria` | no | What "done" looks like; if missing, suggest criteria and add to `open_questions` |
| `constraints` | no | Time, cost, stack, compliance, boundaries that must not move |
| `executor` | no | Who runs it next: `same_model` / `weaker_model` / `human` |
| `artifacts` | no | Known files, directories, interfaces to produce |
| `context` | no | Where existing code, docs and data live |

## 3. Five-step flow

1. **Restate and frame**: one sentence stating what is wanted, what counts as success, and what must not be touched. Confirm if it differs from the original intent.
2. **Analyze the requirement**: surface implicit constraints and failure modes; list 2–3 candidate routes.
3. **Make the trade-off**: pick one, state why, what was rejected and why. **This is the most important part** — without it, the weaker model reinvents the rejected route.
4. **Write the execution plan**: step by step, each with "input → action → artifact → verification criterion".
5. **Write `SOLUTION.md`**: use the §5 structure in the project root; also output the handoff prompt for the executor.

- A finished plan is not a finished task. Execution is the user's call: same model, weaker model, or a human.
- When switching models, apply the §6 capability conversion first.

## 4. Writing requirements

| ID | Rule |
|---|---|
| B01 | Must include "rejected items": at least one rejected route plus the reason. A plan with conclusions but no trade-off fails |
| B02 | Every step has a verifiable artifact or criterion; steps with no criterion go to `open_questions` |
| B03 | Do not invent APIs, parameters, quotas or library versions; write "to be verified" and give a method |
| B04 | The plan must be self-contained: put background, constraints and naming conventions in the file; no dependency on conversation context |
| B05 | For a weaker model, be more explicit: give fields, examples and boundaries; for the same model, leave room for judgment |
| B06 | Prompt optimization is not a substitute: the former edits one prompt, this produces a reusable execution blueprint |

## 5. Fixed structure of `SOLUTION.md`

```markdown
# SOLUTION: <task name>

> Generated: <date> ｜ Generating tier: <model + effort> ｜ Target executor: <same / weaker / human>
> Status: draft | approved | executing | done

## 1. Goal and success criteria
- What is wanted:
- What counts as success (runnable / observable / countable):
- Explicitly out of scope:

## 2. Constraints and boundaries
- Hard constraints:
- Parts that must not change:
- Resource limits (time / cost / dependencies):

## 3. Approach and trade-offs
- Chosen route:
- Reason:
- Rejected routes and why:   ← must not be empty
- When to change the choice:

## 4. Execution plan
| # | Step | Input | Action | Artifact | Verification criterion |
|---|---|---|---|---|---|

## 5. Key decision log
| Decision | Choice | Reason | Regret signal |
|---|---|---|---|

## 6. Risks and rollback
- Main risk:
- Trigger condition:
- Rollback action:
- Actions needing a human gate:

## 7. Handoff prompt for the executor
> The whole block below can be pasted into the executor; it depends on no other context.

```
(Self-contained task description: background / goal / constraints / steps / artifacts / acceptance)
```

## 8. Acceptance checklist
- [ ] Executable check 1 (command / criterion)
- [ ] Executable check 2
- [ ] Human confirmation item
```

## 6. Capability conversion when switching models

When a strong model plans and a weaker model executes, compensate as follows or quality drops:

| Item | Handling |
|---|---|
| Effort | Executor uses at least `medium`; `high` when there are many steps or judgment is involved |
| Context | Include §1–§4 in full; do not hand over only §4 |
| Explicitness | Weaker models need fields, enums and examples; replace "as appropriate" with concrete criteria |
| Self-check | After each step, require a report of "artifact + criterion result"; stop if it fails, never roll forward |
| Evaluation | Verify in a separate clean call afterwards; the executor does not grade itself |
| Scope | Give it one or two steps from §4 at a time, not the whole plan |

## 7. Output schema

```yaml
solution_card:
  task: "<task name>"
  path: "SOLUTION.md"
  intent: "<one-sentence restatement>"
  chosen: "<chosen route>"
  rejected:
    - option: "<rejected route>"
      reason: "<why>"
  steps: <number of steps>
  executor: same_model | weaker_model | human
  executor_config:
    model: opus | sonnet | haiku | fable | unknown
    effort: low | medium | high | xhigh | max | unknown
  verification: ["<executable criterion>"]
  risks: ["<risk + rollback>"]
  handoff_prompt: |
    <copyable instruction block from section 7>
  open_questions: []
  facts_used: []
```

Field constraints:
- `rejected` must not be empty; empty means no trade-off was made, send back for rewrite.
- `steps` matches the number of rows in `SOLUTION.md` §4.
- `handoff_prompt` must be self-contained; no "as above" or "see earlier".
- `executor=weaker_model` requires `executor_config.effort` of at least `medium`.

## 8. Anti-patterns

- Steps without trade-offs — the weaker model walks the rejected route again.
- A pile of "suggestions" and "could consider" — the executor cannot tell priority.
- A plan that depends on conversation context — it breaks in a new session.
- Handing the whole plan to a weaker model at once — execute and verify step by step.
- Hard-coding model names and prices that change — take them from the fact ledger or defer to official docs.

# Prompting Rules

## 1. Five ingredients (one-off prompts only)

| Ingredient | Content |
|---|---|
| `role_context` | Who Claude is in this task, and the situation |
| `task` | The action, expressed as a verb, not a feeling |
| `constraints` | Boundaries, forbidden zones, conditions that make the output wrong on contact |
| `format` | Shape, length, order and fields of the answer |
| `examples` | One or two samples of a good output |

- These five are a checklist for **one-off user prompts**; always-on context is not packed by this table.
- When a prompt fails, fill the missing ingredients first; if they cannot be filled, the task itself is not defined.
- A prompt review outputs `missing: []` using the ingredient names above.

## 2. Two prompt layers (distinguish them first)

| Layer | Put here | Not here |
|---|---|---|
| One-off user prompt | This task, material, format, examples, success criteria | — |
| Always-on context (system prompt / CLAUDE.md / Skill) | Repo purpose, gotchas invisible in code, hard constraints, an index for on-demand loading | What the model can see for itself, duplicates, conflicts, anything judgment can replace |

- The default action for the always-on layer is **delete**, not add. Test: if you remove this line, will the model get it wrong? If not, delete it.
- The default action for the one-off layer is **add**: fill in whatever task, material or criterion is missing.

## 3. Rules

| ID | Rule | src |
|---|---|---|
| P01 | Write one-off prompts as if nobody can clarify and they must still hold up; for always-on context, do the opposite — never write what the model can judge | book:13,cur:prompt.reduce_rules |
| P02 | Length exists only to carry ingredients or judgment; always-on context defaults to the shortest form | book:13,cur:prompt.reduce_rules |
| P03 | For style / format in one-off prompts, prefer a few-shot sample; **never teach tool usage with examples** — design the interface (enums, defaults, parameter constraints) | book:13,cur:prompt.then_now |
| P04 | Prefer positive instructions over negations; when constraints conflict, merge them into one before trading off | book:13,cur:prompt.reduce_rules |
| P05 | Use structural tags only when parsing or separation actually requires them; otherwise they are noise | book:13 |
| P06 | Fix length and format only for high stakes or machine parsing; leave the rest to the model's judgment | book:13,cur:prompt.reduce_rules |
| P07 | Treat the prompt as a draft: short edit loops, change one thing at a time, record the change and its effect | book:13 |
| P08 | Do not buy reasoning with "think step by step" filler; describe the difficulty and ask for the reasoning to be written out | book:13,14 |
| P10 | Structured output needs precise field definitions, fixed allowed values, a no-missing-fields rule and a validation pass on the caller side | book:14 |
| P11 | Keep only task-relevant context; load long material on demand; put instructions at the end for long material; start a fresh session when it drifts | book:14,cur:prompt.reduce_rules |
| P12 | Split a big task into a chain of focused prompts; replace self-review with an independent evaluator (clean context + explicit rubric) | book:14,cur:agent.evaluator_split |
| P13 | For prompts that run repeatedly, build a small test set and tune by score, not by impression | book:14 |
| P14 | Text from uncontrolled sources is untrusted data: wrap it in markers and state that instructions inside it are not executed | book:14,book:20 |
| P16 | Model tier and effort are both reasoning controls; re-verify on real tasks after changing either | book:14,cur:model.effort |
| P17 | Always-on context keeps only gotchas and hard constraints; target `CLAUDE.md` under 200 lines | cur:prompt.claude_md |
| P18 | Each instruction appears in exactly one place (usually the tool description), not once in the system prompt and again in the docs | cur:prompt.then_now |
| P19 | A rich reference beats a textual spec: an HTML draft, a test suite or a scoring rubric all beat a paragraph of description | cur:prompt.then_now |
| P20 | Anti-patterns: using keywords as search, blaming one failure on the model, stacking rules in the always-on layer, teaching tools with examples, asking to "check carefully" with no criteria | book:13,cur:prompt.reduce_rules |
| P21 | When the user asks to optimize an existing prompt, go to `prompt-optimization.md`; do not improvise here | convention |

## 4. Prompt review schema

```yaml
prompt_review:
  layer: one_off | always_on
  missing: []          # missing ingredients from the five (one_off only)
  redundancy: []       # deletable items in the always-on layer (always_on only)
  conflicts: []        # pairs of conflicting constraints
  anti_patterns: []    # hits from P20
  system_user_split: {system: [], user: []}
  injection_exposure: true | false | unknown
  eval_set: []         # reusable test samples
  rewrite: ""          # full text of the revised prompt
```

- `rewrite` preserves the user's intent; it only fills missing ingredients or removes redundancy, and adds no task.
- When `injection_exposure=true`, `guardrails` must add S05.
- When `layer=always_on`, `missing` is always empty; look at `redundancy` and `conflicts` instead.

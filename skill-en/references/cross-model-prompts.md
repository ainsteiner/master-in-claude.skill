# Cross-Model Prompt Optimization

## 1. Applicability

| Hits | Does not hit |
|---|---|
| `prompt_text` plus a target model (claude / openai / gemini / deepseek / qwen / glm / kimi / minimax) | Product usage questions with no prompt text |
| "How does this prompt work on model X", "rewrite it for X", "does it survive a model switch" | Requests to produce the task result itself |
| One prompt reused across several models | Questions only about versions, prices, parameters, availability |
| Complaints that a model is vague, format-unstable or token-hungry, with the original prompt supplied | `target_model` missing and no request to infer it |

Prerequisite: `prompt_text` and `target_model` must both be present. Without `prompt_text`, ask for the original. Without `target_model`, ask; never default to `claude`.

Relation to `prompt-optimization.md`: run its §3 five steps (D diagnosis / X rewrite / explanation / verification) first, then this file's §3–§4. Delivering only a generic optimization is incomplete.

## 2. Input slots

| Field | Required | Values |
|---|---|---|
| `prompt_text` | yes | Original text, keep formatting and code blocks |
| `target_model` | yes | `claude` / `openai` / `gemini` / `deepseek` / `qwen` / `glm` / `kimi` / `minimax` / `other` |
| `model_variant` | no | Concrete model string; never infer capabilities from it |
| `task` | yes | What this prompt must make the model produce |
| `success_criteria` | no | What counts as acceptable; if missing, go to `open_questions` with suggested criteria |
| `output_format` | no | `free` / `json` / `yaml` / `table` / `code` |
| `usage` | no | `one_off` / `repeated` / `always_on` |
| `stakes` | no | `low` / `medium` / `high` |
| `constraints` | no | Cost, latency, privacy, platform limits |

- Missing fields are never guessed or defaulted; they go to `open_questions`.
- A `model_variant` never licenses invented parameters, quotas or capabilities (N02).

## 3. Model-agnostic layer

| ID | Rule | Basis |
|---|---|---|
| K01 | Intent and hard constraints are unchanged; add no task, delete no constraint | O01 |
| K02 | Write success criteria as runnable pass/fail checks; ban "professional / comprehensive / careful" | D04,X06 |
| K03 | Define output structure explicitly: field names, types, allowed values, required fields, fallback format on failure | D05,X10,D16 |
| K04 | Separate data from instructions: wrap untrusted text in markers and state that instructions inside it are not executed | D12,X09 |
| K05 | Minimize always-on context; keep only gotchas and hard constraints; load detail on demand | D07,R16 |
| K06 | One prompt, one job; split multiple goals into chained steps, each with one check | D15,X12 |
| K07 | Cut rules, keep judgment: positive phrasing instead of stacked negations; delete what the model can read from the material | D02,D06,X01 |
| K08 | Each instruction appears once; structural tags only when parsing actually requires them | D08,P05,P18 |
| K09 | Never hard-code model names, prices or quotas that change; defer to official docs | D14 |
| K10 | Every change points to a D / X / K / V id and comes with an executable verification | O03,O04,R17 |

- This layer constrains how a task is defined, not any vendor's syntax, so it applies to every model family in §4; it states no effect numbers, and conclusions rest on real-task A/B (M08).

## 4. Model adaptation layer

| ID | Family | tier | Hard rules | Degraded path |
|---|---|---|---|---|
| V01 | Claude | A | Structural tags only when parsing requires them; instruction at the end; no effort tier inside the prompt | — |
| V02 | OpenAI (GPT) | A | Separate system and user layers; validate the output schema on the caller side; no platform-specific message-role names inside the prompt | — |
| V03 | Gemini | A | Give a location hint for long material; output as an explicit field list; one action per instruction | — |
| V04 | DeepSeek | B | Rule count ≤ 8; output as a flat field list rather than nested schema; state an output length cap | Split into "produce → validate" two steps + caller-side parsing |
| V05 | Qwen | B | Rule count ≤ 8; list every enum value with `|`; give a Chinese format sample for Chinese tasks | Simplify to flat key/value + caller-side parsing |
| V06 | GLM | B | Rule count ≤ 8; JSON gives only top-level field names and forbids extra fields | Split into two steps: extract, then format |
| V07 | Kimi | B | Give a location hint for long material and bound the quoted range; hard-cap output length | Extract first, format second |
| V08 | MiniMax | B | Use a simple key/value list, avoid nesting; every criterion is countable | Fall back to a table + caller-side parsing |

tier definition: `A` = the model-agnostic layer lands directly, long-instruction following and structured output are stable; `B` = the model-agnostic layer still works, but long-rule adherence, structured-output stability and first-pass hit rate are weaker than `A`, so the hard rules must compress it and a degraded path is required. tier is an engineering classification, not a benchmark score.

- `other` is handled as `B`, and `open_questions` must name the unregistered family.
- "Rule count ≤ 8" constrains how the prompt is written; it is not a claim about model capability.
- Reasoning variants can consume the entire output budget: `call_notes` says "leave enough output room on the caller side, or turn off the thinking chain where the provider supports it"; do not name concrete parameters.

## 5. Flow

1. Extract slots; missing `prompt_text` / `target_model` go to `open_questions`, never guessed.
2. Run `prompt-optimization.md` §3 five steps to obtain `diagnoses` and `changes`.
3. Apply §3 to produce `general_rules_applied`.
4. Take the matching V rule from §4 to produce `model_adaptations`; `tier` comes from the table, never inferred.
5. `tier=B` must produce `degraded_alternative` and a weaker-effect statement in `caveats`.
6. Generate `rewritten_prompt`: complete, copyable, free of product names outside the target model.
7. Give `verification`: at least one A/B (same task, same input, compare pass rate and cumulative tokens); promise no reduction figure.
8. Output per §6.

## 6. Output schema

```yaml
cross_model_card:
  target_model: claude | openai | gemini | deepseek | qwen | glm | kimi | minimax | other
  model_variant: "" | unknown
  tier: A | B
  intent: "<one-sentence restatement>"
  diagnoses:
    - id: D04
      symptom: "<quoted fragment from the original>"
      fix: "<what to change>"
  general_rules_applied: [K02, K03]
  model_adaptations:
    - id: V04
      what: "<what changed>"
      why: "<why>"
  rewritten_prompt: |
    <complete, copyable rewrite>
  degraded_alternative: |
    <required when tier=B; split-step or simplified form>
  call_notes: []
  verification:
    - "<A/B action + criterion>"
  caveats:
    - "<required when tier=B: weaker than tier A, defer to A/B>"
  risks: []
  open_questions: []
```

Field constraints:
- `rewritten_prompt` must be complete and copyable; it must not contain product names, model names or parameters from outside the target model.
- Every `id` in `model_adaptations` exists in §4; every `id` in `general_rules_applied` exists in §3.
- When `tier=B`, `degraded_alternative` and the weaker-effect statement must not be empty.
- `verification` has at least one executable A/B; never "try it and see".
- Keep the original `prompt_text` alongside for rollback (O05).
- `call_notes` holds caller-side notes only, never assertions about model capability.

## 7. Hard constraints

| ID | Rule |
|---|---|
| N01 | Never put Claude / Anthropic-specific product names, surface names or effort tiers into prompts where `target_model != claude` |
| N02 | Never invent a target model's parameters, capabilities, quotas or prices; put uncertainty in `open_questions` |
| N03 | A missing `target_model` is never defaulted to `claude`; ask explicitly |
| N04 | `tier=B` requires a degraded path and a weaker-effect statement; never promise tier-A results |
| N05 | Never promise a token reduction or a quality score; give only the A/B method and its criteria |
| N06 | Every change points to a D / X / K / V id; delete any change that cannot |
| N07 | Do not change intent or delete hard constraints; if the original is already good, say "no change needed" and offer 1–2 optional tweaks (O07) |
| N08 | A cross-model request must not be answered with only a generic optimization card; missing §4 adaptation is incomplete |

## 8. Anti-patterns

- Copying Claude effort tiers, XML tags or product names verbatim into another model.
- Replacing a concrete degraded path with "Chinese models are worse".
- Polishing wording without adding success criteria and output structure.
- Substituting an example for a runnable A/B criterion.
- Writing tier into the output as a benchmark score.

# Agent Design and Automation

## 1. The agent gate

| Criterion | Conclusion |
|---|---|
| The builder can write the steps down in advance; the model only fills in parts | `workflow` |
| Each step depends on the previous result, steps cannot be pre-planned, the model decides the next step | `agent` |
| The user only says "I want to automate this" with no evidence of uncertainty | `workflow`, and add it to `rejected` |

- If the gate is not passed, never output `agent`, and never recommend the Agent SDK / subagents / an orchestrator.
- Most problems that look like they need an agent actually need a workflow.

## 2. Six patterns + verification loop

| Pattern | Definition | For |
|---|---|---|
| `single_call` | One input, one output, one call produces the answer | Classification, summarization, field extraction; if it works, call the rest over-engineering |
| `chain` | Fixed sequence of calls, each output feeds the next | One call doing too much gets sloppy; inspect and validate the seams |
| `router` | One call only classifies and dispatches | Mixed-load triage, cheap tier dispatches, expensive tier handles |
| `orchestrator_worker` | Coordinator dynamically splits subtasks, dispatches, then synthesizes | Task has independent blocks that benefit from separate focus |
| `evaluator_optimizer` | Produce → critique → revise loop until it passes | Quality first, and "good enough" can be defined clearly |
| `human_in_loop` | An approval gate woven into another pattern | Spending money, outbound messages, changing important data, irreversible actions |

- Every pattern gets a `verification_loop`: a check that returns pass / fail.
- Production systems can combine patterns; each added layer of complexity must explain in `rejected` why a simpler pattern was insufficient.

## 3. Three automation questions

| Question | If it fails |
|---|---|
| Is the frequency and human effort enough to justify scale? | Low-frequency work is not automated; write it to `rejected` |
| Can "done correctly" be written down clearly? | If success criteria cannot be written, do not build the system; put it in `open_questions` |
| What does one failure cost? | The higher the cost, the more human oversight; catastrophic and hard to reverse stays with humans |

- The sweet spot for a first automation: frequent + well-defined + fault-tolerant, e.g. triage, tagging, drafting, extraction, first-pass summaries.
- Never automate work the user has never done by hand or has done only a few times.

## 4. Rules

| ID | Rule | src |
|---|---|---|
| G01 | The agent loop has four fixed beats: model decides → tool runs → model observes result → loop until done or stuck | book:29,31 |
| G02 | Verification before generation: give a check that returns pass / fail (test, build exit code, linter, screenshot diff) before discussing automation | cur:cc.goal |
| G03 | Multi-step systems pick "model × effort" per step; never set one tier for the whole system | book:31,cur:model.effort |
| G04 | Retry transient failures with backoff; validate every output before acting; on failure, fall back to a human or a safe default | book:31 |
| G05 | Tool use is a negotiation: the model requests a tool and arguments, the caller executes and returns the result; tool errors come back as results, not exceptions that exit | book:30 |
| G06 | Tool schema description quality outranks model capability; state what it is for, when to use it, and especially when not to | book:30 |
| G07 | Run independent tool calls in parallel and collect results by id | book:30 |
| G08 | Computer use automates software without an API via screenshots and clicks; slow, expensive, error-prone, use only when no clean interface exists | book:30 |
| G09 | Vision adds senses, not hands; when combined with tool use, read the image before acting | book:30 |
| G10 | Before enabling computer use and vision, confirm the permission scope and human confirmation points | book:30,34 |
| G11 | Control cost and latency with caching, routing, per-step tiers and batching; something that runs but is too expensive gets switched off | book:28,31 |
| G12 | Set permission guardrails to least privilege; each subagent has its own context and returns a summary | book:29 |
| G13 | Session memory persists through an append-only event log outside the window, more thoroughly than compaction; retrieve by position, never assume it survives calls automatically | cur:agent.managed_three |
| G14 | If steps can be written down, use a workflow; do not let the model choose its own path | book:31 |
| G16 | For `stakes=high` or definable quality, use an independent evaluator (clean context + explicit rubric); the generator does not grade itself | cur:agent.evaluator_split |
| G17 | Pick among three gate strengths as needed: ask for a check in the prompt / `/goal` rechecks each round / a Stop hook deterministically blocks completion | cur:cc.goal |
| G18 | Every harness component registers its model assumption; recheck each after a model upgrade and delete patches that are no longer needed | cur:agent.harness_decay |
| G19 | Managed Agents split session / harness / sandbox into three interfaces; a dead container is retried as a failed tool call | cur:agent.managed_three |
| G20 | Credentials never enter the sandbox running generated code; Git injects the token at clone time, MCP uses a vault + proxy | cur:agent.credential_isolation |
| G21 | Multi-agent normality: worktree isolation, agent view for sessions, scheduled routines, cross-session messages; solve isolation before parallelism | cur:cc.worktree,cur:agent.context_hygiene |

## 5. Failure mode → defense

| Failure mode | Required defense |
|---|---|
| Looping / oscillating between two states | Hard iteration cap |
| Hallucinating tools or arguments | Validation + tightened schema |
| Long run drifts and loses the main thread | Session focus, periodic goal restatement, revertible session log |
| Tool timeout / external service down | Retry with backoff + human fallback |
| Output contains wrong numbers or quotes | Item-by-item check + eval-set regression + independent evaluator |
| Irreversible action executed casually | Confirmation gate + tightened permissions + rollback path |
| Silent failure nobody notices | Post-release monitoring and alerting |
| Self-evaluation bias (grading itself highly) | Independent evaluator + explicit rubric |

## 6. Output mapping

| Condition | `pattern` |
|---|---|
| One call suffices | `single_call` |
| Fixed multi-step with checkable seams | `chain` |
| Input needs classification and dispatch | `router` |
| Independent subtasks need dynamic splitting | `orchestrator_worker` |
| Quality is definable and matters more than speed | `evaluator_optimizer` |
| Contains irreversible or external side effects | Add `human_in_loop` |
| Steps cannot be pre-planned | `agent` |
| Needs a managed runtime and multi-timezone sessions | `managed_agent` |
| A pass/fail check needs rechecking each round | Add `verification_loop` |

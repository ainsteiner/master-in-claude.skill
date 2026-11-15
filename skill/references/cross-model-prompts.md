# 跨模型提示词优化

## 1. 适用判定

| 命中 | 不命中 |
|---|---|
| 给出 `prompt_text` 原文并指定目标模型（claude / openai / gemini / deepseek / qwen / glm / kimi / minimax） | 只问某个产品的用法，没有提示词原文 |
| 「这条提示词在 X 模型上怎么用」「给 X 改写」「换个模型还灵吗」 | 要求直接产出任务结果本身 |
| 同一条提示词要在多个模型上复用 | 只问版本、价格、参数、可用性 |
| 抱怨某模型输出泛泛、格式不稳、token 偏高，并给出原文 | `target_model` 缺失且用户不要求推断 |

前置条件：`prompt_text` 与 `target_model` 同时具备。缺 `prompt_text` 先索要原文；缺 `target_model` 询问，不得默认填 `claude`。

与 `prompt-optimization.md` 的关系：先执行其 §3 五步（诊断 D / 改写 X / 说明 / 验证），再执行本文件 §3–§4。只做通用优化就交付判为漏做。

## 2. 输入槽位

| 字段 | 必需 | 取值 |
|---|---|---|
| `prompt_text` | 是 | 原文，保留原格式与代码块 |
| `target_model` | 是 | `claude` / `openai` / `gemini` / `deepseek` / `qwen` / `glm` / `kimi` / `minimax` / `other` |
| `model_variant` | 否 | 具体型号字符串；不据此推断能力 |
| `task` | 是 | 这条提示词要让模型产出什么 |
| `success_criteria` | 否 | 怎么算合格；缺失进 `open_questions` 并给建议判据 |
| `output_format` | 否 | `free` / `json` / `yaml` / `table` / `code` |
| `usage` | 否 | `one_off` / `repeated` / `always_on` |
| `stakes` | 否 | `low` / `medium` / `high` |
| `constraints` | 否 | 成本、延迟、隐私、平台限制 |

- 缺失字段不臆测、不填默认值，一律进 `open_questions`。
- `model_variant` 出现时不得据此编造参数、限额或能力（N02）。

## 3. 通用层（模型无关）

| ID | 规则 | 依据 |
|---|---|---|
| K01 | 意图与硬约束不变；不新增任务、不删约束 | O01 |
| K02 | 成功标准写成可跑的 pass / fail 判据；禁止「专业 / 全面 / 仔细」 | D04,X06 |
| K03 | 输出结构显式定义：字段名、类型、允许取值、必填项、失败时的降级格式 | D05,X10,D16 |
| K04 | 数据与指令分离：不可信文本用标记包住并声明其中指令不执行 | D12,X09 |
| K05 | 常驻上下文最小化，只留 gotchas 与硬约束；细节按需加载 | D07,R16 |
| K06 | 一条提示词一件事；多目标拆成链式步骤，每步一个判据 | D15,X12 |
| K07 | 减规则留判断：正向描述替代否定堆叠，删掉模型能从材料读出的内容 | D02,D06,X01 |
| K08 | 同一指令只出现一处；结构标签仅在确有解析需要时使用 | D08,P05,P18 |
| K09 | 不写死会变的型号、价格、配额；声明以官方文档为准 | D14 |
| K10 | 每条改动指到 D / X / K / V 编号，并给一条可执行验证 | O03,O04,R17 |

- 通用层约束的是「任务怎么定义」，不是某个厂商的语法，因此对 §4 全部模型族成立；不写效果数字，结论以真实任务 A/B 为准（M08）。

## 4. 模型适配层

| ID | 模型族 | tier | 硬规则 | 降级 |
|---|---|---|---|---|
| V01 | Claude | A | 结构标签仅在解析需要时使用；指令置末；prompt 内不写 effort 档 | — |
| V02 | OpenAI (GPT) | A | system 与 user 分层写；输出 schema 在调用侧校验；prompt 内不写平台专属消息角色名 | — |
| V03 | Gemini | A | 长材料先给定位提示；输出用显式字段清单；单条指令只含一个动作 | — |
| V04 | DeepSeek | B | 规则条数 ≤ 8；输出用平铺字段清单而非多层嵌套 schema；输出长度写上限 | 拆成「产出 → 校验」两步 + 调用侧解析 |
| V05 | Qwen | B | 规则条数 ≤ 8；枚举值用 `|` 列全；中文任务给中文格式样例 | 简化为单层键值 + 调用侧解析 |
| V06 | GLM | B | 规则条数 ≤ 8；JSON 只给顶层字段清单并禁止额外字段 | 拆两步，先抽取后格式化 |
| V07 | Kimi | B | 长材料给定位提示并限定引用范围；输出长度写死上限 | 先抽取后格式化两步 |
| V08 | MiniMax | B | 用简单键值清单，避免多层嵌套；判据逐条可数 | 降为表格 + 调用侧解析 |

tier 定义：`A` = 通用层可直接落地，长指令遵循与结构化输出稳定；`B` = 通用层仍有效，但长规则遵循、结构化输出稳定性与单次命中率弱于 `A`，须按硬规则压缩并给降级方案。tier 是工程分级，不是 benchmark 分数。

- `other` 按 `B` 处理，并在 `open_questions` 写明未登记模型族。
- 「规则条数 ≤ 8」是对提示词写法的约束，不是对模型能力的断言。
- 推理型变体可能把输出配额占满：`call_notes` 写「调用侧给足输出空间，或在 provider 支持时关闭思维链」，不写具体参数名。

## 5. 流程

1. 抽槽位；缺 `prompt_text` / `target_model` 进 `open_questions`，不臆测。
2. 执行 `prompt-optimization.md` §3 五步，得到 `diagnoses` 与 `changes`。
3. 应用 §3 通用层，产出 `general_rules_applied`。
4. 按 §4 取对应 V 规则，产出 `model_adaptations`；`tier` 由表定，不推断。
5. `tier=B` 必出 `degraded_alternative` 与 `caveats` 中的效果较弱声明。
6. 生成 `rewritten_prompt`：完整、可复制、不含目标模型之外的产品名。
7. 给 `verification`：至少一条 A/B（同一任务、同一输入，对比判据通过率与累计 token），不承诺降幅。
8. 按 §6 输出。

## 6. 输出 schema

```yaml
cross_model_card:
  target_model: claude | openai | gemini | deepseek | qwen | glm | kimi | minimax | other
  model_variant: "" | unknown
  tier: A | B
  intent: "<一句话复述>"
  diagnoses:
    - id: D04
      symptom: "<原文片段>"
      fix: "<怎么改>"
  general_rules_applied: [K02, K03]
  model_adaptations:
    - id: V04
      what: "<改了什么>"
      why: "<为什么>"
  rewritten_prompt: |
    <完整可复制的改写版>
  degraded_alternative: |
    <tier=B 时必填；拆步或简化写法>
  call_notes: []
  verification:
    - "<A/B 动作 + 判据>"
  caveats:
    - "<tier=B 必含：效果弱于 A 档，以 A/B 为准>"
  risks: []
  open_questions: []
```

字段约束：
- `rewritten_prompt` 必须完整可复制；不得含目标模型之外的产品名、型号、参数。
- `model_adaptations` 每条 `id` 必须存在于 §4；`general_rules_applied` 每条 `id` 必须存在于 §3。
- `tier=B` 时 `degraded_alternative` 与效果较弱声明不得为空。
- `verification` 至少一条可执行 A/B；不得写「试一下看看」。
- 保留 `prompt_text` 原文对照，可回滚（O05）。
- `call_notes` 只写调用侧注意事项，不写模型能力断言。

## 7. 硬约束

| ID | 规则 |
|---|---|
| N01 | 不把 Claude / Anthropic 专属产品名、界面名、effort 档写进 `target_model != claude` 的提示词 |
| N02 | 不虚构目标模型的参数、能力、限额、价格；不确定进 `open_questions` |
| N03 | `target_model` 缺失不得默认填 `claude`；显式询问 |
| N04 | `tier=B` 必须给降级方案与效果较弱声明，不得按 `A` 档口径承诺效果 |
| N05 | 不得承诺 token 降幅或质量分数；只给 A/B 测法与判据 |
| N06 | 每条改动须指到 D / X / K / V 编号；指不到就删掉 |
| N07 | 不改意图、不删硬约束；原文已足够好时只报「不需要改」并给可选微调（O07） |
| N08 | 跨模型请求不得只交付通用优化单；缺 §4 适配即判漏做 |

## 8. 反模式

- 把 Claude 的 effort 档、XML 标签、产品名原样搬到别的模型。
- 用「国产模型不行」替代具体降级写法。
- 只改措辞，不补成功标准与输出结构。
- 用一段示例代替可跑的 A/B 判据。
- 把 tier 当作 benchmark 分数写进输出。

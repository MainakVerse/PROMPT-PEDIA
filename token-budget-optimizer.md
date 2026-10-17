# Token Budget Optimizer

> Fit prompts and context within a target token budget while preserving task requirements, constraints, and downstream answer quality.

**Status:** Proposed specification  
**Category:** Prompt Compression and Optimization  
**Suggested module:** `packages/compressor/token-budget-optimizer/`  
**Protocol ID:** `TBO-1`

---

## 1. Overview

Token Budget Optimizer (TBO) helps applications manage the input tokens sent to an LLM. Given a prompt, relevant context, a target model, and a token budget, it estimates or measures token usage and selects a strategy to reduce unnecessary context while preserving the information required to complete the task.

TBO is not simply a text-shortening function. It is a budget-aware planner that chooses among leaving text unchanged, removing redundancy, summarizing context, pruning examples, restructuring instructions, or requesting a more appropriate context allocation.

It is designed for applications using OpenAI, Anthropic Claude, Google Gemini, and other LLM providers. Token counting, context limits, pricing, caching rules, and multimodal accounting vary by provider and model; provider-specific adapters should be used wherever possible.

> **Core principle:** Satisfy the token budget without silently sacrificing required meaning or task quality.

A token budget is a constraint, not a reason to delete critical information. If the budget cannot be met safely, TBO should report the conflict or request a larger budget rather than pretend that a lossy prompt is equivalent.

## 2. Goals

- Measure or estimate prompt-token usage for a specified provider and model.
- Fit prompts within a configurable input-token budget.
- Reserve capacity for system instructions, tool schemas, conversation context, and expected output where applicable.
- Remove low-value redundancy before high-value content.
- Preserve hard constraints, exact values, safety requirements, and task intent.
- Account for decoder instructions and other overhead introduced by compression.
- Support priority-aware context selection.
- Fall back to the original prompt when optimization is unsafe or ineffective.
- Report the decisions and trade-offs made by the optimizer.
- Avoid unnecessary LLM calls when deterministic strategies are sufficient.

## 3. Non-goals

TBO does not:

- Guarantee identical output after context pruning.
- Assume character count equals token count.
- Assume all providers tokenize or price inputs identically.
- Automatically truncate the end of a prompt to satisfy a budget.
- Remove safety instructions or required evidence to save tokens.
- Treat every context item as equally important.
- Claim cost savings without measuring the complete request.
- Replace provider context-window limits, output-token settings, or API usage reporting.

## 4. Core concepts

### 4.1 Token budget

The maximum token allocation available to a defined request component or request as a whole.

Budgets should explicitly identify what they include. For example, an input-only budget may exclude generated output, while a total-context budget may reserve capacity for both input and output.

### 4.2 Hard constraints

Information that must not be dropped or altered, including:

- Required and forbidden actions.
- Safety and trust-boundary instructions.
- Exact numbers, dates, units, and identifiers.
- Mandatory output fields or schemas.
- User-specified exclusions and conditions.
- Required sources, citations, or verification.
- Information necessary for a correct tool call.

### 4.3 Soft priorities

Information that may be shortened, summarized, or omitted when appropriate, such as repeated examples, redundant wording, tangential background, and outdated context that is not relevant to the current task.

Soft priorities must be identified carefully; an item should not be treated as optional merely because it appears verbose.

### 4.4 Token measurement

Prefer an official provider/model token-counting facility or the corresponding tokenizer where available. When exact counting is unavailable, use a documented estimate and label it as such.

For multimodal inputs, token accounting may include image, audio, video, or other provider-specific components that cannot be estimated reliably from text alone.

## 5. Input and output contracts

### Input

```ts
export type TokenBudgetInput = {
  prompt: string;
  provider?: "openai" | "anthropic" | "google" | "other";
  model?: string;
  maxInputTokens: number;
  reservedOutputTokens?: number;
  reservedSystemTokens?: number;
  reservedToolTokens?: number;
  mode?: "auto" | "preserve_quality" | "strict_budget";
  contextItems?: ContextItem[];
  decoderOverheadTokens?: number;
};

export type ContextItem = {
  id: string;
  text: string;
  priority: "critical" | "high" | "normal" | "low";
  required?: boolean;
  source?: string;
};
```

### Output

```ts
export type TokenBudgetResult = {
  originalPrompt: string;
  optimizedPrompt: string;
  status: "within_budget" | "optimized" | "budget_conflict" | "fallback";
  tokenCount?: number;
  estimatedTokenCount?: number;
  targetTokenBudget: number;
  reservedTokens: number;
  remainingTokens?: number;
  omittedContextIds: string[];
  transformedContextIds: string[];
  warnings: string[];
  fallbackUsed: boolean;
  measurementMethod: "provider" | "tokenizer" | "estimate" | "unknown";
};
```

The schema is illustrative. Implementations should distinguish measured and estimated counts and should not populate authoritative-looking token values without a valid measurement method.

## 6. Budget calculation

Define the usable input budget before optimizing.

```text
usable_input_budget =
  max_input_tokens
  - reserved_system_tokens
  - reserved_tool_tokens
  - reserved_other_input_tokens
```

If the supplied limit is a total request budget rather than an input-only limit, also reserve capacity for output and any other billed or context-consuming components according to the provider's semantics.

Do not subtract the same component twice. Some provider APIs count system messages, tool definitions, and structured metadata within the same input-token total; others expose additional usage details. The adapter must document its accounting model.

The final candidate should be checked against the actual request structure, not just the text body in isolation.

## 7. Optimization strategy

Apply strategies in increasing order of information loss.

### Stage 1 — Measure

1. Identify the provider and model, if known.
2. Count or estimate tokens for the full request components available to TBO.
3. Account for decoder instructions, schemas, and fixed overhead where applicable.
4. Return the original unchanged if the request is already within budget.

### Stage 2 — Remove safe overhead

- Normalize unnecessary whitespace.
- Remove exact duplicate text where duplication is truly redundant.
- Merge repeated instructions without weakening them.
- Remove irrelevant metadata that the downstream task does not need.
- Avoid adding explanatory labels that cost more tokens than they save.

### Stage 3 — Restructure

- Convert long, repetitive requirements into compact fields.
- Move reusable instructions into a stable shared prefix when the architecture supports it.
- Reference stored context by ID only if the downstream system can actually resolve that reference.
- Preserve the meaning of negation, conditions, priorities, and output schemas.

### Stage 4 — Optimize context

- Rank context items by task relevance and required status.
- Keep required items intact unless a semantics-preserving representation is available.
- Summarize or prune lower-priority context only when the task can still be completed reliably.
- Preserve source references and critical evidence.
- Avoid merging unrelated facts in a way that loses attribution or relationships.

### Stage 5 — Validate and measure again

- Recount the full candidate request.
- Include the cost of any added decoder or summary instructions.
- Check all hard constraints.
- Check whether the downstream model can interpret the transformed representation.
- If possible, run task-specific semantic or regression checks.

### Stage 6 — Accept or fall back

- Accept the candidate if it fits the budget and passes required checks.
- Return the original if it fits and the candidate provides no meaningful benefit.
- Return `budget_conflict` if the target cannot be met without unsafe information loss.
- Use a documented fallback policy when validation fails.

Never silently delete critical context just to make a numeric target appear satisfied.

## 8. Optimization modes

### `auto`

Choose the least aggressive strategy that achieves the budget with acceptable risk.

### `preserve_quality`

Prioritize semantic preservation. Allow the result to exceed the requested budget when meeting it would require unsafe loss, and report the conflict.

### `strict_budget`

Attempt to fit the target budget, but do not violate hard constraints or safety requirements. If the budget cannot be met safely, return a conflict rather than silently dropping mandatory information.

A strict budget does not authorize destructive truncation or semantic corruption.

## 9. Context prioritization

A recommended default priority order is:

1. Trusted system and developer instructions.
2. Safety requirements and trust boundaries.
3. Explicit user goals and hard constraints.
4. Exact values and information necessary for correct execution.
5. Evidence required to answer accurately.
6. Relevant recent context and decisions.
7. Supporting examples and background.
8. Redundant or unrelated context.

This ordering is a starting policy, not a universal rule. Task-specific requirements can change the relevance of particular context items. Trusted instructions must remain distinct from untrusted text even if the latter appears highly relevant.

## 10. Example

### Input

```text
Prompt:
Please research the latest AI regulations in India. Include reliable
citations, distinguish enacted laws from proposed rules, and summarize
the most important changes. Do not treat proposed rules as enacted law.

Maximum input budget:
500 tokens
```

### Potential result

```text
TASK=research+summarize
SCOPE=India; AI regulation; latest
EVIDENCE=reliable citations
DISTINGUISH=enacted vs proposed
FORBIDDEN=present proposed rules as enacted
```

This is only an illustrative transformation. TBO must measure the token count of the complete provider request, including any decoder instructions, and verify that the compact schema is understood by the target model.

If the original request already fits the budget, the optimizer may correctly choose not to transform it.

## 11. TypeScript reference implementation

The following is a minimal budget-checking baseline. It does not perform semantic summarization and deliberately avoids destructive truncation. Replace the example counter with a provider-specific tokenizer or usage-aware implementation.

```ts
export type BudgetStatus =
  | "within_budget"
  | "budget_conflict";

export type BudgetCheck = {
  text: string;
  status: BudgetStatus;
  measuredCharacters: number;
  budget: number;
  warning?: string;
};

// Character count is only a placeholder, not a token count.
export function checkCharacterBudget(
  text: string,
  maxCharacters: number
): BudgetCheck {
  if (text.length <= maxCharacters) {
    return {
      text,
      status: "within_budget",
      measuredCharacters: text.length,
      budget: maxCharacters,
    };
  }

  return {
    text,
    status: "budget_conflict",
    measuredCharacters: text.length,
    budget: maxCharacters,
    warning:
      "No safe compression strategy was applied. Use a tokenizer-aware optimizer.",
  };
}
```

This function intentionally reports a conflict instead of truncating text. A production implementation should use actual token measurement and a staged optimization pipeline.

## 12. Testing requirements

Test at least the following:

- Prompts already within budget.
- Prompts that exceed the budget by a small or large amount.
- Repeated instructions and duplicate context.
- Critical constraints mixed with low-priority background.
- Negation, exclusions, and conditional requirements.
- Dates, currencies, units, identifiers, and exact numeric limits.
- JSON schemas and code blocks.
- Citation and source requirements.
- Multilingual and Unicode text.
- Long conversation histories with relevant earlier decisions.
- Multimodal requests with provider-specific token accounting.
- Decoder overhead that makes shorthand uneconomical.
- Unresolvable context references.
- Cases where no safe optimization can meet the target.

### Required assertions

- The original prompt remains recoverable.
- Hard constraints are never silently discarded.
- The final request is measured after all transformations.
- Estimates are labelled as estimates.
- Strict-budget failures are reported explicitly.
- A smaller token count is not automatically treated as a better result.
- The optimizer does not claim success if the final request exceeds the budget.

## 13. Evaluation metrics

Track:

- Original and final input tokens.
- Net token savings, including added decoder overhead.
- Budget success rate.
- Semantic constraint preservation.
- Downstream task success.
- End-to-end latency.
- Total provider-reported cost.
- Context items omitted or transformed.
- Fallback and budget-conflict rates.
- Regression rate on a fixed evaluation set.

A useful net savings measure is:

```text
net_token_savings =
  baseline_full_request_tokens
  - optimized_full_request_tokens
```

Calculate savings over comparable requests using the same provider, model, tool configuration, and accounting scope.

## 14. Security and privacy

- Do not log raw prompts by default.
- Keep API credentials out of client-side code.
- Preserve trusted/untrusted instruction boundaries.
- Do not discard safety policies to fit a budget.
- Avoid sending irrelevant personal data to external models.
- Treat summaries and compressed context as untrusted until validated.
- Do not use a context reference unless the referenced content is available to the downstream model.
- Define retention and telemetry policies explicitly.

## 15. Roadmap

- [ ] Provider-aware token-count adapters.
- [ ] Full-request budget accounting.
- [ ] Configurable reservations for system, tool, and output tokens.
- [ ] Priority-aware context selection.
- [ ] Conservative redundancy removal.
- [ ] Structured prompt encoding.
- [ ] Semantic validation and fallback.
- [ ] OpenAI, Claude, and Gemini integration tests.
- [ ] Multimodal accounting support where available.
- [ ] Public benchmarks and regression tests.
- [ ] Cost and latency reporting.

## 16. Contribution guidelines

Every contribution should include:

1. A clear problem statement.
2. Example inputs and expected outputs.
3. Tests covering hard constraints and edge cases.
4. Token measurements and the measurement method.
5. Downstream quality results where applicable.
6. Known limitations and provider compatibility notes.

Do not claim universal cost savings or accuracy preservation without reproducible evidence.

## 17. License

Use the license selected by the root repository. Ensure that the repository contains the actual license file before distributing this module.

---

**Token Budget Optimizer (TBO-1): allocate tokens deliberately, preserve essential information, and report when a budget cannot be met safely.**

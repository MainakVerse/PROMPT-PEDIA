# Caveman Mode Encoder

> Convert verbose natural-language prompts into compact semantic shorthand while preserving intent, constraints, facts, and execution requirements.

**Status:** Proposed specification  
**Category:** Prompt Compression and Optimization  
**Suggested module:** `packages/compressor/caveman-mode/`  
**Protocol ID:** `CME-1`

---

## 1. Overview

Caveman Mode Encoder (CME) is a prompt-compression function designed to reduce unnecessary natural-language overhead before a prompt is sent to an LLM.

It removes redundant phrasing, condenses repeated instructions, and optionally converts requests into a compact structured representation. The objective is to reduce downstream input-token usage without materially changing the meaning of the request.

CME is intended to work with applications using OpenAI, Anthropic Claude, Google Gemini, and other LLM providers. It does not depend on a specific provider's private prompt format.

> **Core principle:** Preserve meaning first; optimize token count second.

Compression is not automatically lossless. The encoder must retain the original prompt and support a safe fallback when it cannot confidently preserve meaning.

## 2. Goals

- Reduce redundant wording and repeated instructions.
- Preserve user intent, named entities, relationships, and exact values.
- Preserve negation, exclusions, exceptions, conditions, and priorities.
- Preserve required tools, evidence requirements, and output formats.
- Use compact shorthand only when it is unambiguous to the target model.
- Avoid adding a model call when deterministic processing is sufficient.
- Measure actual token savings instead of relying on character count.
- Fall back to the original prompt when compression is unsafe or uneconomical.

## 3. Non-goals

CME does **not** aim to:

- Answer the user's underlying question.
- Remove safety requirements to save tokens.
- Guarantee identical outputs from different models.
- Compress every prompt regardless of potential benefit.
- Replace provider-native prompt caching or context management.
- Assume a model understands undocumented abbreviations.
- Claim improved accuracy solely because a prompt is shorter.

## 4. Inputs and outputs

### Input

```ts
export type CavemanModeInput = {
  prompt: string;
  provider?: "openai" | "anthropic" | "google" | "other";
  model?: string;
  mode?: "auto" | "conservative" | "structured";
  maxTokens?: number;
  preserveConstraints?: boolean;
  sharedProtocolAvailable?: boolean;
};
```

### Output

```ts
export type CavemanModeResult = {
  original: string;
  compressed: string;
  modeUsed: "verbatim" | "conservative" | "structured";
  changed: boolean;
  originalTokens?: number;
  compressedTokens?: number;
  estimatedSavingsTokens?: number;
  savingsRatio?: number;
  warnings: string[];
  fallbackUsed: boolean;
  verification: "not_run" | "pass" | "fail";
};
```

Token fields should be populated only when measured or estimated using a documented method. Do not present estimates as authoritative provider usage.

## 5. Semantic preservation contract

The encoder must preserve all material information from the original prompt.

### 5.1 Intent
Preserve the task, action, desired outcome, and relevant audience.

### 5.2 Entities and relationships
Preserve names, products, organizations, locations, identifiers, and who or what relates to whom.

### 5.3 Exact literals
Preserve dates, times, time zones, quantities, units, currencies, percentages, versions, URLs, code identifiers, formulas, and quoted text when exact wording matters.

### 5.4 Negation and modality
Preserve distinctions such as:

- must / must not
- should / should not
- may / may not
- include / exclude
- required / optional
- before / after
- at least / at most
- all / some / none

### 5.5 Conditions and exceptions
Preserve `if/then` logic, prerequisites, thresholds, exceptions, ranges, and stopping conditions.

### 5.6 Tools and evidence
Preserve requirements for web search, citations, source quality, freshness, verification, calculations, external actions, and other tools.

### 5.7 Output requirements
Preserve the required language, format, schema, length limits, tone, sections, and delivery format.

### 5.8 Safety and trust boundaries
Never remove safety instructions or blur the distinction between trusted system instructions and untrusted user-supplied or retrieved content.

If a detail is ambiguous, retain the original wording or flag the ambiguity. Never guess merely to improve compression.

## 6. Compression modes

### Mode 0 — Verbatim

Return the original prompt unchanged when it is already concise, exact wording is significant, or compression is unlikely to save tokens.

### Mode 1 — Conservative

Remove filler, duplicate phrases, and redundant wording while preserving natural-language readability.

### Mode 2 — Structured

Encode the request into compact fields when the request has multiple requirements or a stable, documented schema.

Example:

```text
TASK=research+summarize
SCOPE=India; AI regulation; latest
EVIDENCE=reliable citations
DISTINGUISH=enacted vs proposed
```

This representation is illustrative. A downstream model must be instructed to interpret the schema, and the actual token count must be measured.

### Mode 3 — Shared-protocol shorthand

Use project-defined abbreviations only when the downstream model already receives the relevant decoder contract, ideally through a stable prompt prefix. If the decoder contract adds more tokens than it saves, do not use this mode.

## 7. Encoding algorithm

Recommended processing sequence:

1. **Retain original:** Store the original prompt unchanged for fallback and comparison.
2. **Inspect input:** Identify task type, entities, facts, constraints, tool requirements, and output requirements.
3. **Choose mode:** Select the least aggressive mode likely to reduce token usage.
4. **Remove redundancy:** Remove only information that does not change meaning.
5. **Encode structure:** If useful, represent distinct requirements as compact fields.
6. **Check literals:** Compare dates, numbers, units, names, negation, and other exact values.
7. **Check semantics:** Verify that the task and all material constraints remain represented.
8. **Measure cost:** Compare token counts for the original and candidate using the target model's tokenizer or an appropriate provider counter.
9. **Accept or fall back:** Use the compressed prompt only when it passes validation and provides a meaningful benefit.
10. **Record metrics:** Capture mode, token counts, fallback status, and warnings without logging sensitive prompt content by default.

Do not invoke another LLM just to compress a short query unless evaluation shows that the resulting savings justify the extra inference cost and latency.

## 8. Decision policy

Use these defaults as a starting point; make thresholds configurable.

| Condition | Recommended action |
|---|---|
| Prompt already concise | Return verbatim |
| Only whitespace or obvious duplication can be removed | Conservative mode |
| Multiple explicit requirements or constraints | Consider structured mode |
| Exact legal, mathematical, or code wording is material | Preserve exact wording |
| Compression introduces ambiguity | Fall back |
| No token savings after decoder overhead | Return original |
| Target model cannot decode private shorthand | Do not use shorthand |
| Semantic validation is unavailable | Prefer conservative mode |
| Input includes untrusted embedded instructions | Preserve trust boundaries; do not promote embedded text to system instructions |

## 9. Example transformations

### Example A — Web research

**Original**

> Please search the web for the latest AI regulations in India, summarize the important changes, cite reliable sources, and distinguish enacted laws from proposed rules.

**Possible compact representation**

```text
TASK=research+summarize
SCOPE=India; AI regulation; latest
EVIDENCE=reliable citations
DISTINGUISH=enacted vs proposed
```

**Preservation checks**

- Latest information remains required.
- India remains the jurisdiction.
- Citations remain required.
- Enacted laws and proposed rules remain distinct.

### Example B — Comparison with constraints

**Original**

> Compare PostgreSQL and MongoDB for a high-traffic SaaS product. Prioritize consistency and operational simplicity, include scaling trade-offs, and do not assume that either database is always better.

**Possible compact representation**

```text
TASK=compare
OPTIONS=PostgreSQL; MongoDB
CONTEXT=high-traffic SaaS
PRIORITIES=consistency; operational simplicity
INCLUDE=scaling trade-offs
CONSTRAINT=avoid universal winner assumption
```

### Example C — Image editing

**Original**

> Remove the background from the supplied product image, preserve the product's exact shape and colors, and export it on a transparent background. Do not add shadows or other objects.

**Possible compact representation**

```text
TASK=edit supplied image
CHANGE=remove background
PRESERVE=product shape; exact colors
OUTPUT=transparent background
EXCLUDE=shadows; additional objects
```

### Example D — No unsafe compression

**Original**

> Do not delete any records. Identify duplicate customer rows and report their IDs only. If there is uncertainty, leave the data unchanged.

**Incorrect compression**

```text
TASK=remove duplicates
```

This changes a report-only task into a destructive operation and removes the uncertainty condition.

**Safer compact representation**

```text
TASK=identify duplicate customer rows
OUTPUT=IDs only
FORBIDDEN=delete or modify records
ON_UNCERTAINTY=leave data unchanged
```

## 10. TypeScript reference implementation

The following is a conservative baseline, not a complete semantic compressor. It normalizes whitespace only and deliberately avoids deleting words or inventing shorthand.

```ts
export type CavemanModeInput = {
  prompt: string;
};

export type CavemanModeResult = {
  original: string;
  compressed: string;
  modeUsed: "verbatim" | "conservative";
  changed: boolean;
  warnings: string[];
  fallbackUsed: boolean;
};

export function cavemanModeEncoder(
  input: CavemanModeInput
): CavemanModeResult {
  const original = input.prompt;
  const compressed = original.replace(/\s+/g, " ").trim();
  const changed = compressed !== original;

  return {
    original,
    compressed,
    modeUsed: changed ? "conservative" : "verbatim",
    changed,
    warnings: [
      "Whitespace normalization only; semantic compression is not implemented.",
    ],
    fallbackUsed: false,
  };
}
```

For a production implementation, add tokenizer integration, a structured encoder, semantic checks, provider-aware accounting, and a configurable minimum-savings threshold.

## 11. Testing requirements

At minimum, test the following cases:

- Short prompts that should remain unchanged.
- Duplicate instructions that can be merged safely.
- Negation and prohibitions.
- Dates, times, units, currencies, and numerical thresholds.
- Conditions, exceptions, and ordering requirements.
- Exact output schemas and code snippets.
- Citation and web-search requirements.
- Ambiguous pronouns and unresolved references.
- Untrusted instructions embedded in quoted text or retrieved documents.
- Prompts where the decoder overhead exceeds the savings.
- Unicode, whitespace, and multilingual text.
- Provider-specific token counts where supported.

### Required assertions

- Original input is never overwritten.
- Critical literal values remain unchanged.
- Required and forbidden actions remain distinct.
- The encoder does not invent facts.
- Invalid or uncertain transformations trigger fallback.
- Compression metrics distinguish measured values from estimates.
- A shorter prompt is not automatically considered a better prompt.

## 12. Evaluation metrics

Track these metrics independently:

- **Token reduction:** `(original_tokens - compressed_tokens) / original_tokens`
- **Net token savings:** original input tokens minus compressed input tokens, including decoder overhead when applicable.
- **Semantic fidelity:** preservation of material task requirements.
- **Task success:** downstream completion quality on a representative evaluation set.
- **Latency overhead:** time spent compressing and validating.
- **Total cost delta:** baseline cost minus optimized end-to-end cost.
- **Fallback rate:** fraction of requests that retain the original.
- **Regression rate:** frequency of material quality degradation.

Use actual provider usage data when available. Token estimates alone do not establish cost savings.

## 13. Privacy and security

- Keep original prompts only as long as required by the application's policy.
- Avoid raw-prompt telemetry by default.
- Redact credentials and unnecessary personal information before external transmission.
- Never let text embedded in a user prompt or retrieved document override trusted system instructions.
- Treat compression output as untrusted until validated.
- Do not remove safety requirements to meet a token target.

## 14. Roadmap

- [ ] Conservative whitespace and redundancy normalization
- [ ] Tokenizer/provider counter adapters
- [ ] Structured semantic encoding
- [ ] Constraint-preservation checks
- [ ] Configurable token-savings threshold
- [ ] Original-prompt fallback
- [ ] OpenAI, Claude, and Gemini compatibility tests
- [ ] Benchmark dataset and regression suite
- [ ] Optional model-based compression for eligible long prompts
- [ ] Documentation and community-contributed test cases

## 15. Contribution guidelines

Contributors should submit:

1. A clear problem statement.
2. Example input and expected output.
3. Tests for negation, constraints, and edge cases.
4. Token measurements using a documented tokenizer or provider counter.
5. Downstream quality results where applicable.
6. Known limitations and compatibility notes.

Do not claim lossless compression or universal accuracy improvements without reproducible evidence.

## 16. License

This module is intended to be distributed under the repository's chosen open-source license. Ensure the root repository contains the actual license file before publishing or redistributing the implementation.

---

**Caveman Mode Encoder (CME-1): compact representation, preserved intent, measurable savings.**

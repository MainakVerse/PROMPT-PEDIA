# Prompt Toolkit

### Open-source prompt compression, enhancement, and optimization for every LLM workflow.

**Compress tokens. Preserve meaning. Improve reliability. Create better AI workflows.**

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![TypeScript](https://img.shields.io/badge/TypeScript-Ready-3178C6.svg)](https://www.typescriptlang.org/)
[![Python](https://img.shields.io/badge/Python-FastAPI-009688.svg)](https://fastapi.tiangolo.com/)
[![Providers](https://img.shields.io/badge/LLMs-OpenAI%20%7C%20Claude%20%7C%20Gemini-8A2BE2.svg)]()
[![Contributions Welcome](https://img.shields.io/badge/Contributions-Welcome-brightgreen.svg)](CONTRIBUTING.md)

Prompt Toolkit is an open-source collection of composable functions for compressing, enhancing, validating, and optimizing prompts used with large language models (LLMs).

It aims to help developers reduce unnecessary token consumption, improve instruction clarity, preserve critical constraints, and build more efficient AI applications without unnecessarily sacrificing output quality.

From semantic prompt compression and research agents to image generation, video production, music, voice synthesis, SEO, coding, and scenario analysis, Prompt Toolkit provides a shared foundation for reusable prompt-engineering capabilities.

**Our philosophy:** optimize the representation, not the intelligence.

> [!IMPORTANT]
> Prompt compression is not guaranteed to be lossless. Every optimization should be evaluated for semantic fidelity, downstream quality, token usage, and latency. This project prioritizes correctness over aggressive compression.

---

## Table of Contents

- [Why Prompt Toolkit?](#why-prompt-toolkit)
- [Core Objectives](#core-objectives)
- [Features](#features)
- [The 100 Enhancement Functions](#the-100-enhancement-functions)
- [Architecture](#architecture)
- [How It Works](#how-it-works)
- [Technology Stack](#technology-stack)
- [Getting Started](#getting-started)
- [Usage Examples](#usage-examples)
- [Provider Compatibility](#provider-compatibility)
- [Compression and Accuracy Strategy](#compression-and-accuracy-strategy)
- [Benchmarking](#benchmarking)
- [Security and Privacy](#security-and-privacy)
- [Roadmap](#roadmap)
- [Contributing](#contributing)
- [Community Guidelines](#community-guidelines)
- [License](#license)

---

## Why Prompt Toolkit?

LLM applications often send much more information than is necessary to perform a task.

A seemingly simple user query may be accompanied by:

- Large system prompts.
- Repeated conversation history.
- Redundant agent instructions.
- Long tool descriptions.
- Entire web pages when only a few passages are relevant.
- Repeated examples and output specifications.
- Unnecessary intermediate agent calls.

These overheads can increase inference costs, latency, and context-window consumption.

Prompt Toolkit aims to make these workflows more efficient through reusable, testable optimization functions.

### The problem

Consider an AI application that performs web research and generates a concise answer. Its input may contain a short user query, a large system prompt, browsing instructions, conversation history, and several retrieved documents.

Simply shortening the user's question may save very little. The larger opportunity may lie in repeated instructions and oversized context.

### The solution

Prompt Toolkit provides functions that can:

1. Remove redundant wording.
2. Encode requests into compact semantic representations.
3. Preserve constraints, entities, relationships, and logical conditions.
4. Select relevant context for a specific task.
5. Improve prompt structure for specialized use cases.
6. Validate that compression has not changed critical meaning.
7. Measure token usage, latency, and total API cost.
8. Revert to the original prompt when optimization is unsafe.

### Intended outcomes

- Lower avoidable token consumption.
- More consistent prompt construction.
- Better traceability of prompt transformations.
- Reusable enhancements across different AI providers.
- Testable prompt-engineering workflows.
- Community-driven improvements for specialized use cases.

These are project objectives, not guaranteed results for every model, prompt, or workload.

---

## Core Objectives

### 1. Semantic preservation

Compression must preserve the user's intended task, entities, numerical values, constraints, exceptions, and expected output.

### 2. Provider independence

Enhancement logic should be reusable across OpenAI, Anthropic Claude, Google Gemini, and other supported models.

### 3. Composability

Developers should be able to combine multiple enhancements into a pipeline instead of relying on a single, monolithic prompt.

### 4. Measurable optimization

Token savings must be measured using the target model's tokenizer or authoritative usage metadata wherever possible.

### 5. Quality-aware fallback

When compression is ambiguous, ineffective, or unsafe, the original prompt should remain available.

### 6. Open collaboration

Developers, researchers, designers, writers, and AI practitioners should be able to contribute specialized enhancements, evaluation datasets, and prompt packs.

---

## Features

| Feature | Description |
|---|---|
| Semantic compression | Shortens prompts while preserving task meaning. |
| Caveman-style shorthand | Converts verbose instructions into compact, structured representations. |
| Token budgeting | Estimates and manages prompt-token budgets. |
| Context optimization | Removes redundant history and irrelevant retrieved content. |
| Accuracy enhancement | Clarifies requirements and preserves important constraints. |
| Research enhancement | Improves search queries, evidence extraction, and synthesis. |
| Agent optimization | Reduces unnecessary tool calls and repeated agent steps. |
| Creative prompt generation | Structures image, video, audio, and visual-design requests. |
| Writing and SEO | Enhances blog briefs, outlines, brand voice, and marketing prompts. |
| Scenario analysis | Structures assumptions, risks, forecasts, and decision criteria. |
| Evaluation framework | Compares prompt versions for cost, latency, and quality. |
| Provider adapters | Supports provider-specific integrations behind a common interface. |

---

## The 100 Enhancement Functions

The toolkit is organized into ten functional categories.

### Category 1 — Prompt Compression and Optimization

1. Semantic Prompt Compressor
2. Caveman Mode Encoder
3. Token Budget Optimizer
4. Redundancy Remover
5. Instruction Deduplicator
6. Context Compressor
7. System Prompt Optimizer
8. Few-Shot Example Pruner
9. Prompt Segment Prioritizer
10. Semantic Diff Validator
11. Cross-Model Prompt Encoder
12. Compression Fallback Controller

### Category 2 — Accuracy and Reliability

13. Ambiguity Resolver
14. Constraint Preserver
15. Instruction Conflict Detector
16. Requirement Completeness Checker
17. Hallucination Risk Reducer
18. Citation Requirement Enhancer
19. Fact Verification Planner
20. Confidence Calibration Enhancer
21. Output Schema Enforcer
22. Edge-Case Expander
23. Instruction Adherence Auditor
24. Prompt Regression Tester

### Category 3 — Research and Knowledge Synthesis

25. Research Query Expander
26. Search Query Compressor
27. Source Quality Optimizer
28. Recency Requirement Injector
29. Evidence Extraction Prompt Generator
30. Multi-Source Synthesis Enhancer
31. Contradiction Finder
32. Research Gap Detector
33. Deep Research Planner
34. Citation-to-Claim Mapper
35. RAG Context Optimizer
36. Research Stopping Controller

### Category 4 — Agents, Tools and Coding

37. Agent Plan Optimizer
38. Tool Selection Optimizer
39. Tool Call Argument Optimizer
40. Agent Loop Reducer
41. Agent Stop-Condition Generator
42. Parallel Task Decomposer
43. Workflow Dependency Mapper
44. API Request Prompt Builder
45. Code Generation Enhancer
46. Code Review Prompt Generator
47. Debugging Prompt Enhancer
48. SQL and Data Query Optimizer

### Category 5 — Image Generation and Visual Design

49. Image Prompt Enhancer
50. Composition Optimizer
51. Lighting and Color Director
52. Style Consistency Enforcer
53. Character Consistency Prompt Builder
54. Negative Prompt Generator
55. Image Editing Instruction Optimizer
56. Product Photography Prompt Builder
57. UI Mockup Prompt Generator
58. Image Reference Alignment Enhancer

### Category 6 — Video Generation and Animation

59. Video Prompt Enhancer
60. Shot List Generator
61. Storyboard Prompt Generator
62. Camera Motion Controller
63. Temporal Consistency Enhancer
64. Character Motion Director
65. Scene Transition Optimizer
66. Video Duration Planner
67. Lip-Sync and Dialogue Alignment Prompt
68. Video Artifact Prevention Prompt

### Category 7 — Music, Sound and Voice

69. Music Prompt Enhancer
70. Genre and Mood Selector
71. Instrumentation Planner
72. Tempo and Structure Controller
73. Lyric Structure Generator
74. Voice Generation Prompt Enhancer
75. Prosody and Emotion Director
76. Pronunciation Dictionary Builder
77. Sound Effect Prompt Generator
78. Audio Continuity Enhancer

### Category 8 — Blogging, SEO and Marketing

79. Blog Brief Generator
80. SEO Search Intent Optimizer
81. Topical Coverage Enhancer
82. Outline Quality Optimizer
83. Brand Voice Enforcer
84. Readability Optimizer
85. Factual Writing Safeguard
86. Metadata and Snippet Generator
87. Conversion Copy Optimizer
88. Content Repurposing Prompt Builder

### Category 9 — Analysis and Decision Intelligence

89. Scenario Analysis Prompt Generator
90. What-If Analysis Enhancer
91. Sensitivity Analysis Planner
92. Risk Assessment Prompt Generator
93. Decision Matrix Builder
94. Forecasting Prompt Enhancer
95. Root Cause Analysis Prompt
96. Counterargument and Falsification Enhancer

### Category 10 — Developer and Community Tooling

97. Prompt Injection Risk Analyzer
98. Sensitive Data Redactor
99. Prompt Cost and Latency Estimator
100. Prompt Benchmark and Leaderboard Generator

These are the initial capability definitions. Each function will become production-ready through implementation, documentation, test coverage, and measurable evaluation. Listing a function does not imply that its implementation is already available.

---

## Architecture

Prompt Toolkit is designed as a modular, provider-independent system.

```text
                    User Application
                           |
                           v
                 Next.js / TypeScript
                           |
                           v
                  Prompt Toolkit Core
                           |
             +-------------+-------------+
             |             |             |
             v             v             v
         Compression    Enhancement   Validation
             |             |             |
             +-------------+-------------+
                           |
                           v
                 Provider Adapter Layer
                           |
             +-------------+-------------+
             |             |             |
             v             v             v
          OpenAI        Anthropic      Google
                           |
                           v
                 LLM / Tool Execution
                           |
                           v
                Evaluation and Metrics
```

### Design principles

- Keep deterministic transformations local whenever practical.
- Use model inference only when semantic understanding requires it.
- Reuse stable prompt prefixes and provider-supported caching when appropriate.
- Preserve original inputs for recovery and auditing.
- Separate enhancement logic from provider-specific API calls.
- Make token measurement and evaluation independent of prompt generation.

The architecture can support either direct TypeScript usage or a Python API for applications that need Python-based libraries.

---

## How It Works

### Step 1 — Receive the original request

Accept the user's query, relevant context, required constraints, and optional token budget.

### Step 2 — Analyze the task

Identify the intent, entities, relationships, exact values, exclusions, tool requirements, evidence requirements, and desired output.

### Step 3 — Select an enhancement

Choose the least aggressive optimization suitable for the task.

Suggested compression levels:

- **Level 0 — Verbatim:** retain the original.
- **Level 1 — Redundancy removal:** remove unnecessary repetition.
- **Level 2 — Semantic rephrasing:** use concise, unambiguous language.
- **Level 3 — Structured encoding:** represent complex requirements as fields.
- **Level 4 — Shared-protocol shorthand:** use documented compact notation.

### Step 4 — Validate the transformation

Check that critical constraints and semantics remain intact.

### Step 5 — Measure the result

Measure token counts, estimated cost, processing latency, and applicable quality metrics.

### Step 6 — Execute or fall back

Use the optimized prompt only when the transformation is acceptable. Otherwise, retain the original prompt.

---

## Technology Stack

The project is designed around the following technologies.

| Component | Preferred technology |
|---|---|
| Web application | Next.js |
| Language | TypeScript |
| API service | Python + FastAPI |
| Schema validation | Zod / Pydantic |
| LLM providers | OpenAI, Anthropic Claude, Google Gemini |
| Testing | Vitest / pytest |
| Evaluation | Custom benchmarks and provider usage metadata |
| Deployment | Vercel for supported workloads; alternative Python hosting when appropriate |
| Version control | Git + GitHub |
| Documentation | Markdown |

These are intended technologies, not a claim that every component has already been implemented.

---

## Getting Started

> The commands below describe the intended developer workflow. They should be updated to match the actual package structure and published releases.

### Prerequisites

- Node.js LTS
- Python 3.12 or another version supported by the selected dependencies
- npm, pnpm, or another configured package manager
- Git
- API credentials for any providers you intend to use

### Clone the repository

```bash
git clone https://github.com/YOUR_USERNAME/prompt-toolkit.git
cd prompt-toolkit
```

### Install frontend dependencies

```bash
npm install
```

### Set up the Python service

```bash
python -m venv .venv
```

Activate the environment:

```bash
# Linux / macOS
source .venv/bin/activate

# Windows PowerShell
.venv\Scripts\Activate.ps1
```

Install the Python dependencies once the backend dependency manifest is available:

```bash
pip install -r requirements.txt
```

### Configure environment variables

Create a local `.env.local` file for the Next.js application.

```dotenv
OPENAI_API_KEY=
ANTHROPIC_API_KEY=
GOOGLE_API_KEY=

COMPRESSOR_API_URL=http://127.0.0.1:8000
```

Only configure the providers you use. Never commit real API keys or expose secret credentials through client-side environment variables.

### Start the development servers

Next.js:

```bash
npm run dev
```

FastAPI, if the backend uses `api/index.py`:

```bash
uvicorn api.index:app --reload --port 8000
```

The exact import path depends on the implemented backend structure.

---

## Usage Examples

### 1. Semantic compression

Input:

```text
Please search the web for the latest AI regulations in India,
summarize the important changes, cite reliable sources, and
distinguish enacted laws from proposed rules.
```

Possible compressed representation:

```text
TASK=research+summarize
SCOPE=India; AI regulation; latest
EVIDENCE=reliable citations
DISTINGUISH=enacted vs proposed
```

The output is illustrative. Actual token savings must be measured, and the target model must understand the encoding.

### 2. Conservative TypeScript interface

```typescript
interface EnhancementInput {
  prompt: string;
  provider?: "openai" | "anthropic" | "google";
  model?: string;
  maxTokens?: number;
  preserveConstraints?: boolean;
}

interface EnhancementResult {
  prompt: string;
  originalTokens?: number;
  enhancedTokens?: number;
  warnings: string[];
  fallbackUsed: boolean;
}
```

An enhancement should return enough metadata to support debugging, measurement, and safe fallback.

### 3. Compose multiple enhancements

A research pipeline could combine:

```text
Research Query Compressor
          |
          v
Source Quality Optimizer
          |
          v
RAG Context Optimizer
          |
          v
Citation-to-Claim Mapper
          |
          v
Semantic Fidelity / Quality Checks
```

Composition should remain configurable. Not every task needs every enhancement.

---

## Provider Compatibility

Prompt Toolkit aims to support a common interface across multiple providers.

| Provider | Intended support |
|---|---|
| OpenAI | Chat and reasoning models, supported caching and tools |
| Anthropic Claude | Claude models, supported caching and tools |
| Google Gemini | Gemini models, supported caching and tools |
| Other providers | Adapter-based integration where feasible |

### Important compatibility notes

- Different providers use different tokenizers and token-counting mechanisms.
- Prompt caching has provider-specific requirements.
- Tool calling and structured output formats differ.
- Multimodal models may account for image, audio, and video inputs differently.
- A compact encoding that works well with one model may not work equally well with another.

Provider-specific testing is therefore essential.

---

## Compression and Accuracy Strategy

Prompt compression is not simply the removal of words.

A successful transformation must preserve:

- User intent.
- Entities and relationships.
- Negation and exceptions.
- Dates, units, numerical values, and identifiers.
- Required and prohibited actions.
- Citation and evidence requirements.
- Output schemas and formatting constraints.
- Safety requirements and trust boundaries.

### When not to compress

Retain the original prompt when:

- The original is already concise.
- Exact wording is legally, technically, or operationally significant.
- Compression introduces ambiguity.
- A structured representation costs more tokens than it saves.
- The downstream model cannot reliably decode the representation.
- Evaluation indicates a material loss in task performance.

### Measure total cost, not just prompt length

Total cost may include:

- The original model's input tokens.
- Cached and uncached input tokens.
- Output and reasoning tokens where billed.
- Search and retrieval charges.
- Additional compressor-model calls.
- Validation or reranking calls.
- Retries caused by failed transformations.

A compressor that reduces prompt length but adds a more expensive model call may increase total cost.

---

## Benchmarking

Every enhancement should be evaluated before being promoted as an optimization.

### Recommended metrics

| Metric | Purpose |
|---|---|
| Input token count | Measures prompt size |
| Compression ratio | Measures relative token reduction |
| Total API cost | Measures actual economic benefit |
| End-to-end latency | Measures user-facing speed |
| Task completion rate | Measures successful outcomes |
| Constraint preservation | Measures whether critical requirements survive |
| Citation accuracy | Measures evidence traceability for research tasks |
| Regression rate | Detects quality degradation |
| Fallback rate | Shows how often optimization is rejected |

### Suggested benchmark protocol

1. Build a representative set of real prompts.
2. Execute the baseline workflow.
3. Execute the enhanced workflow using the same model and task.
4. Compare token counts and total provider-reported costs.
5. Evaluate correctness, constraint preservation, and task completion.
6. Report results separately for each provider and task category.
7. Publish the test set, evaluation method, and limitations.

Do not report a compression ratio as proof of improved accuracy. Compression and answer quality are separate metrics.

---

## Security and Privacy

Prompt Toolkit should treat security as part of prompt engineering rather than an optional add-on.

- Never hardcode provider API keys.
- Never expose secrets to browser-side code.
- Treat retrieved webpages and user-supplied documents as untrusted input.
- Preserve the distinction between trusted instructions and untrusted content.
- Avoid sending unnecessary personal information to external models.
- Redact secrets before transmission when appropriate.
- Do not log raw prompts by default without an explicit privacy policy.
- Document retention, telemetry, and third-party processing behavior.
- Validate tool arguments and structured model outputs.
- Use access controls and rate limits for hosted API deployments.

Compression must never remove a safety instruction merely because it appears repetitive.

---

## Roadmap

### Phase 1 — Core engine

- [ ] Define common enhancement interfaces.
- [ ] Implement conservative semantic compression.
- [ ] Implement constraint-preservation checks.
- [ ] Add token-count measurement.
- [ ] Add semantic-diff and fallback mechanisms.
- [ ] Add unit tests and sample prompts.

### Phase 2 — Provider adapters

- [ ] Add OpenAI adapter.
- [ ] Add Anthropic adapter.
- [ ] Add Google Gemini adapter.
- [ ] Implement provider-specific usage accounting.
- [ ] Add caching support where available.
- [ ] Document compatibility and limitations.

### Phase 3 — Research and agents

- [ ] Research query optimization.
- [ ] RAG context compression.
- [ ] Citation-to-claim mapping.
- [ ] Agent loop and tool-call optimization.
- [ ] Research stopping conditions.
- [ ] Prompt injection risk analysis.

### Phase 4 — Creative prompt packs

- [ ] Image generation.
- [ ] Video generation.
- [ ] Music and voice generation.
- [ ] UI mockup generation.
- [ ] Blogging and SEO.
- [ ] Marketing and content repurposing.

### Phase 5 — Evaluation and community

- [ ] Public benchmark suite.
- [ ] Regression testing framework.
- [ ] Community prompt-pack registry.
- [ ] Contribution guidelines and review templates.
- [ ] Versioned enhancement catalog.
- [ ] Reproducible performance reports.

Roadmap items are planned work and should be checked against the current repository before being presented as completed.

---

## Contributing

Contributions are welcome from developers, researchers, AI practitioners, writers, designers, and other community members.

You can contribute by:

- Implementing a new enhancement function.
- Improving an existing prompt.
- Adding provider compatibility.
- Creating domain-specific prompt packs.
- Writing test cases and benchmark datasets.
- Reporting regressions or semantic failures.
- Improving documentation.
- Reviewing pull requests.

### Contribution requirements

Every proposed enhancement should include:

1. A clear description of the problem it solves.
2. Its input and output contract.
3. Example inputs and expected outputs.
4. Unit tests for normal and edge cases.
5. Evidence that critical constraints are preserved.
6. Token and latency measurements where applicable.
7. Known limitations and provider compatibility notes.

See [CONTRIBUTING.md](CONTRIBUTING.md) for the contribution workflow.

---

## Community Guidelines

We aim to maintain an open, welcoming, and technically rigorous community.

- Be respectful and constructive.
- Share reproducible examples when reporting issues.
- Distinguish measured results from estimates.
- Do not claim universal accuracy improvements without evidence.
- Respect licensing and attribution requirements.
- Avoid publishing secrets, private user data, or sensitive prompts without authorization.
- Explain trade-offs when an optimization reduces cost but may affect quality.

---

## License

Prompt Toolkit is intended to be released under the MIT License.

The final repository should include a `LICENSE` file containing the complete license text. Until that file is committed, the intended license should not be treated as an already granted permission to use or redistribute the code.

---

## Acknowledgments

Prompt Toolkit builds on the broader open-source AI ecosystem and draws inspiration from research and tooling in prompt compression, prompt optimization, evaluation, and model orchestration.

Relevant projects to explore include:

- [LLMLingua](https://github.com/microsoft/LLMLingua) — prompt compression.
- [DSPy](https://github.com/stanfordnlp/dspy) — programming and optimization of language-model pipelines.
- [Promptfoo](https://github.com/promptfoo/promptfoo) — LLM evaluation and testing.

Please consult the individual projects' licenses and documentation before incorporating their code or dependencies.

---

## Join the Project

Want to help make AI applications more efficient, reliable, and accessible?

- Open an issue to suggest a capability.
- Submit a pull request with a tested enhancement.
- Contribute domain-specific prompt packs.
- Help evaluate compression quality across providers.
- Share reproducible cost and latency benchmarks.

**Better prompts should not require more tokens than necessary.**

Let's build an open-source ecosystem for efficient, reliable, and reusable prompt engineering.

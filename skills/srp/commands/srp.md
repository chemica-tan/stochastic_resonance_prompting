---
description: Export session discussion into a distributable SRP prompt for multi-AI execution
argument-hint: "[title] - short snake_case topic name (e.g., calculator_redesign)"
---

# SRP Prompt Exporter

## Process

### Step 1: Determine title and date

- Date: today's date as YYYYMMDD
- Title: from user argument, or ask. Use snake_case, keep short.
- Folder path: `srp_sessions/YYYYMMDD_title/`
- Prompt file: `srp_sessions/YYYYMMDD_title/YYYYMMDD_title_srp.md`

### Step 2: Distill the session discussion

Review the current conversation and extract all information needed
to compose the prompt. The receiving AI is a coding agent with full
file system access but zero context from this session.

**Extraction checklist** — verify each before proceeding to Step 3:

- [ ] Objective: what must be produced and why?
- [ ] Domain knowledge: what does a reader need to know to avoid invalid output?
- [ ] Design decisions: what was tried, rejected, or constrained?
- [ ] File references: which files must the agent read?
- [ ] Constraints: what rules cannot be inferred from code alone?
- [ ] Evaluation criteria: how will the output be judged?

**Thin-session guard**: If the conversation lacks enough substance to
fill §2 Background with project-specific content (not generic filler),
warn the user: "This session may not have enough discussion to produce
a useful SRP prompt. Continue anyway?"

**Multiple topics**: If the session covered distinct, separable topics,
ask the user whether to produce one combined prompt or multiple focused
prompts (one per topic). Prefer focused prompts — they produce more
comparable outputs across agents.

### Step 3: Compose the unified prompt

Write the SRP prompt file following the **Prompt Template** below.
Every section is mandatory unless marked (optional).

### Step 4: Create folder and file

1. Create the folder `srp_sessions/YYYYMMDD_title/`
2. Write the prompt file
3. Present the prompt to the user for review

### Step 5: Present the one-liners

After the user approves the prompt, present **per-model one-liners**
with the assigned serial number and model name pre-filled.
Each one-liner is copy-pasteable into the respective model's interface.

Example (4-model SRP):

```
#01 gpt_5.2:
srp_sessions/YYYYMMDD_title/YYYYMMDD_title_srp.md を読み、そこに記載されたタスクを実行してください。あなたの連番は 01、モデル名は gpt_5.2 です。

#02 gpt_5.3_codex:
srp_sessions/YYYYMMDD_title/YYYYMMDD_title_srp.md を読み、そこに記載されたタスクを実行してください。あなたの連番は 02、モデル名は gpt_5.3_codex です。

#03 gemini_3.0_pro:
srp_sessions/YYYYMMDD_title/YYYYMMDD_title_srp.md を読み、そこに記載されたタスクを実行してください。あなたの連番は 03、モデル名は gemini_3.0_pro です。

#04 claude_opus_4.6:
srp_sessions/YYYYMMDD_title/YYYYMMDD_title_srp.md を読み、そこに記載されたタスクを実行してください。あなたの連番は 04、モデル名は claude_opus_4.6 です。
```

The serial number and model name tell the agent what to substitute
for `[NN]` and `[YOUR-MODEL-NAME]` in §8.

---

## Prompt Template

The SRP prompt markdown **MUST** follow this structure.
All sections are mandatory unless noted. Constraint levels use
RFC 2119 semantics: **MUST**, **SHOULD**, **MAY**.

````markdown
# [Task Title]

**Date**: YYYY-MM-DD
**Prompt ID**: YYYYMMDD_title_srp

---

## 1. Objective

State the task in 2-4 sentences. Be specific and actionable.
No ambiguity about what "done" looks like.

- **MUST** include: what to produce
- **MUST** include: why this task exists (motivation)
- **MUST NOT** reference any prior conversation or session context

## 2. Background

Provide all domain knowledge and project context the agent needs.
Assume the reader knows nothing about this project.

Subdivide with `###` as needed. Use tables for structured data.
Use code blocks for exact values, paths, formulas, and identifiers.

### 2.1 Project Overview
{Brief description of the project, its purpose, key design principles}

### 2.2 Domain Context
{Domain-specific knowledge required to execute the task correctly.
Domain-specific concepts, definitions, terminology, etc.}

### 2.3 Design Decisions & History
{Relevant past decisions that constrain the current task.
Why things are the way they are. What was tried and rejected.}

> **Guideline**: If an AI not knowing X would lead it to produce
> invalid output, X belongs in this section.

## 3. Scope

### In Scope
- {Concrete item 1}
- {Concrete item 2}

### Out of Scope
- {Concrete exclusion 1 — and why it's excluded}
- {Concrete exclusion 2}

> **Guideline**: Explicit exclusions prevent scope creep.
> State the boundary, not just the interior.

## 4. Constraints

Non-negotiable rules. Violating any **MUST** invalidates the output.

| ID | Level | Constraint |
|----|-------|------------|
| C1 | **MUST** | {e.g., Use Decimal, never float, for all numerical computation} |
| C2 | **MUST** | {e.g., Do not modify the authentication flow} |
| C3 | **SHOULD** | {e.g., Follow existing code patterns in src/auth/} |
| C4 | **MAY** | {e.g., Propose alternative approaches if justified} |

> **Guideline**: Every project-specific rule that the agent cannot
> infer from reading code alone belongs here. Err on the side of
> over-specifying. An agent that violates a silent assumption
> wastes the entire SRP round for that slot.

## 5. Input Materials

Files the agent **MUST** read before starting work.
Paths are relative to project root.

| # | Path | What it contains | Why to read it |
|---|------|------------------|----------------|
| 1 | `src/auth/login.py` | Login handler | Reference implementation |
| 2 | `docs/architecture.md` | System architecture | Design constraints |
| ... | ... | ... | ... |

> **Guideline**: List every file. Don't say "read the src/ directory."
> Explicit paths prevent agents from reading too much or too little.

## 6. Guiding Questions

Numbered questions the agent **MUST** address in their report.
These structure the analysis and ensure comparability across agents.

1. {Specific, answerable question}
2. {Specific, answerable question}
3. ...

> **Guideline**: Questions **MUST** be concrete enough that two
> agents answering the same question produce comparable (if different)
> answers. Avoid "what do you think about X?" — prefer
> "what are the trade-offs between A and B for X?"

## 7. Deliverable Specification

### 7.1 Required Deliverables

#### Deliverable A: Report (mandatory)

The output report **MUST** contain these sections in this order:

1. **Executive Summary** (3-5 sentences)
2. **Findings per Guiding Question** (one subsection per question from §6)
3. **Recommendations** (prioritized, actionable)
4. **Free Commentary** (mandatory) — your observations, concerns, ideas,
   or dissenting opinions that don't fit the above sections.
   This section has no constraints on content or length.
   Write freely. This section **MUST** appear even if brief.

#### Deliverable B+: Code/Data artifacts (if applicable)

{If the task produces code, CSV, config, or other non-report files,
define each artifact here with its output path.}

> **CRITICAL — Artifact Naming Convention**:
>
> All artifact filenames **MUST** follow this pattern:
> ```
> YYYYMMDD_{artifact_name}_{NN}_{YOUR-MODEL-NAME}.{ext}
> ```
> - `{NN}`: 2-digit serial number assigned in §8 (01-99)
> - `{YOUR-MODEL-NAME}`: model identifier (e.g., `gpt_5.2`, `claude_opus_4.6`)
>
> This prevents overwrites both across models AND across multiple runs
> of the same model.
>
> Artifacts are written to the **session folder**, NOT to production paths.
> The production path is populated only after audit.
>
> **Good**: `srp_sessions/YYYYMMDD_title/YYYYMMDD_groups_01_gpt_5.2.csv`
> **Bad**: `src/data/groups.csv` (shared path, will be overwritten)
> **Bad**: `groups_gpt_5.2.csv` (no serial, no date — unidentifiable when moved)

### 7.2 Formatting Rules

- Use markdown with `##` / `###` hierarchy
- Use tables for structured comparisons
- Use code blocks for paths, formulas, identifiers
- Cite specific file paths and line numbers when referencing code
- State confidence level (high/medium/low) for each recommendation

## 8. Output Instructions

Your assigned serial number is **[NN]** and your model identifier is **[YOUR-MODEL-NAME]**.

Create your report as:
```
srp_sessions/YYYYMMDD_title/YYYYMMDD_title_[NN]_[YOUR-MODEL-NAME].md
```

If §7 defines additional artifacts (CSV, code, etc.), name them as:
```
YYYYMMDD_{artifact_name}_{NN}_{YOUR-MODEL-NAME}.{ext}
```

Replace `[NN]` and `[YOUR-MODEL-NAME]` with the values assigned above.

**MUST NOT** modify any existing project source files.
Only create files in the session folder with your serial number and model name in the filename.
````

---

## Composition Rules

When composing the prompt from session discussion, follow these rules:

### AI-Readability

- **Separate context from action.** §2-3 are "understand this." §6-7 are "do this."
  Never mix informational statements with imperatives in the same section.
- **No ambiguous pronouns.** Repeat the noun. "Modify the calculator" not "modify it."
- **No nested conditionals in prose.** Break "if A and B but not C unless D"
  into a bulleted list or pseudo-code.
- **No double negatives.** "Do not skip validation" → "Always validate."
- **Concrete over abstract.** "Reduce coupling" → "AuthService MUST NOT import from database directly."
- **Define before use.** If a term (MECE, SCREAM, PMQS) appears in §4+,
  it must be defined in §2.

### Completeness

- **Every silent assumption is a bug.** If violating an assumption would
  produce invalid output, it must be stated in §4 Constraints.
- **Input Materials must be exhaustive.** If an agent needs a file to
  produce correct output, it must be listed in §5.
- **Guiding Questions must be enumerated.** Free-form "investigate X"
  leads to incomparable outputs. Numbered questions lead to
  structured, comparable answers.

### Language

- **Default: Japanese.** Write the SRP prompt markdown and the one-liner
  in Japanese. Technical terms, code identifiers, file paths, and
  section headings (§1–§8) remain in their original form.
- **English only when explicitly requested.** If the user says to write
  in English, switch both the prompt markdown and the one-liner to English.
  Always keep the two in the same language.

### Artifact Safety

- **Never use production paths for SRP output.** All deliverables
  (reports AND code/data artifacts) go to the session folder.
  Production paths (e.g., `src/*/data/`) are populated only after
  human-supervised audit.
- **Every artifact path must contain `{NN}_{YOUR-MODEL-NAME}`.** This is
  enforced by the template. When composing §7, verify that every file
  the agent is asked to create follows this convention.
- **Serial numbers are assigned in §8.** The prompt author lists the
  intended model slots in §8 and assigns NN = 01, 02, ... in order.
  When the same model runs multiple times, each run gets a distinct NN.

### SRP Integrity

- **No model-specific tuning.** The prompt is identical for all agents
  (§1-§7). Only §8 differs (serial number and model name).
- **No fallback behavior.** Unless the user explicitly requests it,
  do not include "if you cannot do X, try Y" instructions.
- **Free Commentary is mandatory.** §7.1 item 4 must always be present.
  This is where stochastic diversity expresses itself most naturally.

---

## Example

User types: `/srp calculator_redesign`

Creates:
```
srp_sessions/20260204_calculator_redesign/
└── 20260204_calculator_redesign_srp.md
```

One-liner for distribution:
```
srp_sessions/20260204_calculator_redesign/20260204_calculator_redesign_srp.md を読み、そこに記載されたタスクを実行してください。
```

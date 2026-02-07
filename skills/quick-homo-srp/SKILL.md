---
name: quick-homo-srp
description: Generate SRP prompt and auto-execute via Codex CLI in parallel. Homogeneous SRP for fast, low-friction multi-instance analysis.
---

# Quick Homogeneous SRP (HSRP)

Generates an SRP prompt from the current session discussion and immediately
dispatches it to multiple Codex CLI instances in parallel.

## What this does

1. Distills the session discussion into an SRP prompt (same template as `/srp`)
2. Launches N Codex CLI instances, each receiving the prompt via stdin
3. Monitors each instance and reports completion

## Key difference from /srp

- `/srp`: Generates prompt, presents one-liner for **manual** multi-model distribution
- `/quick-homo-srp`: Generates prompt, **auto-executes** via Codex CLI (same or mixed OpenAI models)

## Defaults

- **Model**: Codex CLI default x4 (omits `-m`; specify explicitly to override)
- **Instances**: 4
- All arguments are natural language and optional

## Output Structure

```
srp_sessions/YYYYMMDD_title/
+-- YYYYMMDD_title_srp.md              # The SRP prompt
+-- YYYYMMDD_title_1_gpt_5.2_codex.md  # Instance 1 report
+-- YYYYMMDD_title_2_gpt_5.2_codex.md  # Instance 2 report
+-- YYYYMMDD_title_3_gpt_5.2.md        # Instance 3 (if mixed models)
+-- YYYYMMDD_title_4_gpt_5.2.md        # Instance 4
```

## Prerequisites

- **[Codex CLI](https://github.com/openai/codex)**: OpenAI's CLI tool
- **Authentication**: Subscription (ChatGPT Plus/Pro/Team/Enterprise) or `OPENAI_API_KEY`

## Available Commands

- **/quick-homo-srp [natural language args]** - Generate and auto-execute HSRP

See commands/quick-homo-srp.md for details.

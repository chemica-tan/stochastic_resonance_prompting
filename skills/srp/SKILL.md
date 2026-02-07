---
name: srp
description: Export session discussion into a distributable SRP prompt for multi-AI execution.
---

# Stochastic Resonance Prompting - Prompt Exporter

Packages the current session's discussion into a single, self-contained prompt
that can be copy-pasted to any AI agent without modification.

## What this does

After brainstorming/planning in a session, `/srp` crystallizes the discussion into:

1. A folder under `srp_sessions/`
2. A unified prompt file that any AI can execute independently

## SRP Core Principle

**Same prompt to all AIs.** Do not customize per model.
Stochastic fluctuation in LLM outputs naturally produces diversity.

## Output Structure

```
srp_sessions/YYYYMMDD_title/
├── YYYYMMDD_title_srp.md          # The unified prompt
├── YYYYMMDD_title_claude_opus_4.5.md    # (created by each AI)
├── YYYYMMDD_title_gemini_3.0_pro.md
├── YYYYMMDD_title_gpt_5.2.md
└── ...
```

## Available Commands

- **/srp [title]** - Export current discussion as distributable SRP prompt

See commands/srp.md for details.

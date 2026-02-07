---
description: Generate SRP prompt and auto-execute via Codex CLI in parallel
argument-hint: "[natural language: models, counts, title] or [@path/to/prompt.md models counts]"
---

# Quick Homogeneous SRP (HSRP)

## Overview

This skill dispatches an SRP prompt to multiple Codex CLI instances in parallel.

**Two modes**:
1. **Generate mode** (default): Generate SRP prompt from current session
2. **File mode**: Use an existing SRP prompt file (when `@path` is provided)

---

## Step 0: Detect mode

Check if the argument starts with `@`:

- `@path/to/prompt.md` → **File mode**: Use existing prompt file
- Otherwise → **Generate mode**: Generate from session

**File mode examples**:
```
/quick-homo-srp @srp_sessions/20260205_parser_solver/20260205_parser_solver_srp.md
/quick-homo-srp @srp_sessions/20260205_parser_solver/20260205_parser_solver_srp.md codex 4つ
/quick-homo-srp @srp_sessions/20260205_foo/20260205_foo_srp.md gpt-5.2 x3
```

If **File mode**, skip to Step 1b. Otherwise proceed to Step 1a.

---

## Step 1a: Parse arguments (Generate mode)

Interpret the user's natural language input to determine:

| Parameter | Default | Examples |
|-----------|---------|----------|
| **models + counts** | Codex CLI default x4 | "codex 3つとgpt-5.2 1つ", "gpt-5.2 x2", "3つ" |
| **title** | Ask the user | "calculator_redesign", "smarts_audit" |
| **language** | User's session language | Explicit request overrides (e.g., "in English", "日本語で") |
| **reasoning** | Codex CLI default (recommended: `xhigh`) | "medium", "high", "xhigh", "extra high", "extreme high" |

**Parsing rules**:
- If no model is mentioned, omit `-m` to use Codex CLI's default model
- If no count is mentioned, use 4
- If only a number is given (e.g., "3つ"), use default model with that count
- "codex" alone means use Codex CLI default (omit `-m`)
- Total instances must be 2-8. Warn if outside this range.
- Title uses snake_case. If not provided in the argument, ask the user.
- **Language**: Default to the user's session language. Override if explicitly requested:
  - e.g., "in English", "English prompt", "日本語で", "en français" etc.
- **Reasoning level**: Default is `xhigh`. Detect from natural language:
  - `medium`: "medium", "ミディアム"
  - `high`: "high", "ハイ"
  - `xhigh`: "xhigh", "extra high", "extreme high", "extremely high", "エクストラハイ"
  - Maps to codex `-c model_reasoning_effort="..."`

---

## Step 1b: Parse arguments (File mode)

Extract from the argument:

| Parameter | Source | Default |
|-----------|--------|---------|
| **prompt_file** | `@path` (strip the `@`) | Required |
| **models + counts** | Remaining text after `@path` | Codex CLI default x4 |
| **title** | Derive from filename | e.g., `20260205_srp_parser_solver_prompt.md` → `srp_parser_solver` |
| **language** | Explicit request (e.g., "in English") | User's session language |
| **reasoning** | "medium", "high", "xhigh" etc. | `xhigh` (default) |

**Output folder**: Always `srp_sessions/YYYYMMDD_title/` (same as Generate mode)

**Validation**:
1. Check that the prompt file exists
2. Read the prompt file content
3. If the file doesn't follow SRP template (no §1-§7), warn but proceed

**Title derivation**:
- Strip date prefix (YYYYMMDD_)
- Strip `_srp.md` or `_prompt.md` or `.md` suffix
- Use remaining text as title

**Original file handling**:
- **Move** (not copy) the original prompt file to the sessions folder
- Rename to `YYYYMMDD_title_srp.md` for consistency
- Example: `srp_sessions/20260205_parser_solver/20260205_parser_solver_srp.md` stays in place if already in sessions folder

**Model name normalization** (for filenames):
- `gpt-5.2` -> `gpt_5.2`
- `o3` -> `o3`
- When using Codex CLI default, query the actual model name from the output for filename
- Replace hyphens and dots with underscores

## Step 2: Confirm execution plan

Before generating anything, show the user a brief confirmation:

```
HSRP Execution Plan:
  Title:    YYYYMMDD_title
  Folder:   srp_sessions/YYYYMMDD_title/
  Reasoning: xhigh
  Instances:
    #1  gpt-5.2-codex
    #2  gpt-5.2-codex
    #3  gpt-5.2-codex
    #4  gpt-5.2-codex
  Total: 4 Codex sessions
```

Wait for user approval before proceeding.

## Step 3: Get SRP prompt

**File mode**: Read the prompt file content as the base SRP prompt.

**Generate mode**: Follow the **exact same process** as `/srp` Steps 2-3 (distill session, compose prompt).

### Language Handling

| Language | Prompt Language | Default |
|----------|-----------------|---------|
| Japanese (default) | Write all §1-§7 content in Japanese | Yes |
| English ("英語で" etc.) | Write all §1-§7 content in English | No |

**File mode with language override**: If English is requested but the prompt file is in Japanese, add a note at the top of §8 instructing instances to respond in English despite the Japanese prompt.

Use the SRP Prompt Template from `/srp` (§1-§8) with one modification:

**§8 is replaced with an HSRP-aware version** — see the HSRP §8 Template below.

### HSRP §8 Template

For each instance with serial NN (2-digit, 01-99) and model_name M, §8 reads:

````markdown
## 8. Output Instructions

Your assigned serial number is **NN** and your model identifier is **MODEL_NAME**.

Create your report as:
```
srp_sessions/YYYYMMDD_title/YYYYMMDD_title_NN_MODEL_NAME.md
```

If §7 defines additional artifacts (CSV, code, etc.), name them as:
```
{artifact_name}_{NN}_{MODEL_NAME}.{ext}
```

**MUST NOT** modify any existing project source files.
Only create files in the session folder with your serial number and model name in the filename.
````

**Important**: The base SRP prompt (§1-§7) is identical across all instances.
Only §8 differs (serial number and model name).

Write the base SRP prompt (with a generic §8 placeholder) to:
```
srp_sessions/YYYYMMDD_title/YYYYMMDD_title_srp.md
```

## Step 4: Create instance-specific prompts and launch

For each instance, create the full prompt by combining:
1. The base SRP prompt (§1-§7) from Step 3
2. The instance-specific §8

Then launch each Codex CLI instance as a background Bash process.

### Codex CLI command format (CRITICAL)

```bash
codex exec --full-auto -C "$PROJECT_ROOT" -c model_reasoning_effort="REASONING_LEVEL" - <<'PROMPT_EOF'
[Full SRP prompt with instance-specific §8]
PROMPT_EOF
```

**Options**:
| Option | Purpose | Required | Example |
|--------|---------|----------|---------|
| `exec` | Non-interactive mode | Yes | `codex exec` |
| `--full-auto` | Sandboxed auto-execution | Yes | |
| `-C DIR` | Working directory | Yes | `-C "$PROJECT_ROOT"` |
| `-m MODEL` | Model selection | No (omit to use Codex CLI default) | `-m gpt-5.2` |
| `-c model_reasoning_effort` | Reasoning level | No (default: `xhigh`) | `-c model_reasoning_effort="xhigh"` |
| `-` (trailing) | Read prompt from stdin | Yes | Required when using heredoc |

**Reasoning level values**: `medium`, `high`, `xhigh` (default: `xhigh`)

**Note on `-o` option**:
The `-o FILE` (output-last-message) option exists but is not currently used.
The current approach instructs Codex to create files directly via the prompt.
This may switch to the `-o` approach in the future.

**WRONG** (will fail):
```bash
# Missing 'exec' subcommand
codex --model gpt-5.2 --full-auto ...

# Non-existent option
codex exec --output-markdown FILE ...

# Missing trailing dash for stdin
codex exec --full-auto -m MODEL <<'EOF'
```

**CORRECT** (default model):
```bash
codex exec --full-auto -C "$PROJECT_ROOT" -c model_reasoning_effort="xhigh" - <<'PROMPT_EOF'
[prompt content with §8 file creation instructions]
PROMPT_EOF
```

**CORRECT** (explicit model):
```bash
codex exec --full-auto -C "$PROJECT_ROOT" -m gpt-5.2 -c model_reasoning_effort="xhigh" - <<'PROMPT_EOF'
[prompt content with §8 file creation instructions]
PROMPT_EOF
```

**Each launch command** should be a separate Bash tool call with `run_in_background: true`
so Claude can monitor them individually.

### Model ID mapping

When the user explicitly specifies a model, pass the exact model ID to `-m`:
- `gpt-5.2` -> `-m gpt-5.2`
- `o3` -> `-m o3`

When no model is specified, omit `-m` entirely to use Codex CLI's default.

## Step 5: Monitor and report

After launching all instances:

1. Inform the user that N instances are running
2. Periodically check if output files have been created:
   ```bash
   ls srp_sessions/YYYYMMDD_title/YYYYMMDD_title_*_*.md 2>/dev/null
   ```
3. When checking background tasks, use `TaskOutput` with `block: false` to poll status
4. As each instance completes, report it:
   ```
   [2/4 complete] Instance #01 (gpt-5.2-codex) finished -> YYYYMMDD_title_01_gpt_5.2_codex.md
   ```
5. When all instances are done, show a summary:
   ```
   HSRP Complete:
     #01  gpt_5.2_codex  -> YYYYMMDD_title_01_gpt_5.2_codex.md  [done]
     #02  gpt_5.2_codex  -> YYYYMMDD_title_02_gpt_5.2_codex.md  [done]
     #03  gpt_5.2_codex  -> YYYYMMDD_title_03_gpt_5.2_codex.md  [done]
     #04  gpt_5.2_codex  -> YYYYMMDD_title_04_gpt_5.2_codex.md  [done]
   ```

---

## Notes

- **No auto-analysis**: After completion, the user manually reviews and synthesizes.
- **No timeout**: If an instance hangs, the user kills it manually.
- **MCP servers**: Do not disable. Codex config already has Serena/Cipher/Sequential.
- **Reasoning effort**: Configurable via natural language (`medium`, `high`, `xhigh`). Default: `xhigh`.
- **SRP integrity**: §1-§7 are identical across instances. Only §8 (output path) differs.

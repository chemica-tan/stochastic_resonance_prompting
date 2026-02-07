# Stochastic Resonance Prompting (SRP)

Send the same prompt to multiple AIs, compare their stochastically varying outputs, and surface structures invisible to any single model.

Inspired by **Stochastic Resonance** in physics — adding noise to a weak signal can paradoxically make it easier to detect. SRP applies this principle to LLM prompting. When all four AIs agree, that's strong consensus. When one diverges, that's a branch point worth investigating. Without comparison, you never realize alternatives existed.

## Skills

| Skill | Description | Dependencies |
|-------|-------------|--------------|
| `/srp` | Generate an SRP prompt from session discussion, output one-liners for distribution to each AI. **Manual.** | None (zero dependencies) |
| `/quick-homo-srp` | Generate SRP prompt + auto-execute in parallel via Codex CLI. **Auto.** | [Codex CLI](https://github.com/openai/codex) |

## Installation

### Option A: Ask Claude Code to install it

In a Claude Code session, just say:

> Install the SRP skills from https://github.com/chemica-tan/stochastic_resonance_prompting

Claude Code will clone the repo, copy the skill files into `.claude/skills/`, and clean up.

### Option B: Manual

```bash
git clone https://github.com/chemica-tan/stochastic_resonance_prompting.git

# Manual version only
cp -r stochastic_resonance_prompting/skills/srp /path/to/your-project/.claude/skills/

# Auto version (optional)
cp -r stochastic_resonance_prompting/skills/quick-homo-srp /path/to/your-project/.claude/skills/
```

Restart Claude Code (or start a new session) — `/srp` and `/quick-homo-srp` will be available as slash commands.

## Customization

| Item | Default | Where to change |
|------|---------|-----------------|
| Output path | `srp_sessions/` | All path references in `commands/*.md` |
| Default model | Codex CLI default x4 | `commands/quick-homo-srp.md` defaults |
| Prompt language | Japanese | Language section in `commands/*.md` |
| Reasoning level | `xhigh` | `commands/quick-homo-srp.md` reasoning parameter |

Changing the output path is the most common customization. Search for `srp_sessions` in the skill files and replace with your preferred path.

## Requirements

- **`/srp` (manual)**: No dependencies — works with any Claude Code session. The generated prompt is a plain markdown file; copy-paste it into any AI.
- **`/quick-homo-srp` (auto)**: Requires [Codex CLI](https://github.com/openai/codex). Authenticate via subscription login (ChatGPT Plus/Pro/Team/Enterprise) or `OPENAI_API_KEY` environment variable.

## Related Articles

1. **[note] [Stochastic Resonance Prompting: AI-collaborative refactoring — 1550 to 950 lines in 50 minutes](https://note.com/chemica_tan/n/n92f4ee61a831)** (June 2025, Japanese) — First publication of SRP
2. **[Zenn] [Sent the same prompt to 4 AIs — discovered answers invisible to any single one](https://zenn.dev/chemica_tan/articles/9e45560a8d3d6a)** (February 2026, Japanese) — SRP in practice

## License

[MIT](LICENSE)

---

# Stochastic Resonance Prompting (SRP)

同じプロンプトを複数のAIに投げ、確率的にゆらぐ出力を比較することで、単一AIでは見えない構造を浮かび上がらせる手法です。

物理学の**確率共鳴（Stochastic Resonance）**から着想を得ています。微弱な信号にノイズを加えると、かえって検出しやすくなる——この原理をLLMプロンプティングに応用しました。4つのAIが全員同じことを言えば強い合意、1つだけ違えば検討に値する分岐点。比較しなければ、選択肢があったこと自体に気づけません。

## スキル一覧

| スキル | 概要 | 依存環境 |
|--------|------|----------|
| `/srp` | セッションの議論からSRPプロンプトを生成し、各AIに配布するワンライナーを出力。**手動版**。 | なし（環境依存ゼロ） |
| `/quick-homo-srp` | SRPプロンプト生成 + Codex CLIで並列自動実行。**自動版**。 | [Codex CLI](https://github.com/openai/codex) |

## 導入方法

### 方法A: Claude Codeに頼む

Claude Codeのセッションで、こう言うだけです：

> https://github.com/chemica-tan/stochastic_resonance_prompting からSRPスキルをインストールして

リポジトリのクローンからスキルファイルのコピー、後片付けまで全部やってくれます。

### 方法B: 手動

```bash
# リポジトリをクローン
git clone https://github.com/chemica-tan/stochastic_resonance_prompting.git

# 手動版のみ導入
cp -r stochastic_resonance_prompting/skills/srp /path/to/your-project/.claude/skills/

# 自動版も導入する場合
cp -r stochastic_resonance_prompting/skills/quick-homo-srp /path/to/your-project/.claude/skills/
```

Claude Codeを再起動（またはセッションを新規開始）すれば、`/srp` や `/quick-homo-srp` がスラッシュコマンドとして認識されます。

## カスタマイズ

| 項目 | デフォルト | 変更箇所 |
|------|-----------|---------|
| 出力パス | `srp_sessions/` | 各 `commands/*.md` 内のパス定義を一括置換 |
| デフォルトモデル | Codex CLIのデフォルト x4 | `commands/quick-homo-srp.md` 内のデフォルト値 |
| プロンプト言語 | 日本語 | 各 `commands/*.md` 内の Language セクション |
| 推論レベル | `xhigh` | `commands/quick-homo-srp.md` 内の reasoning パラメータ |

出力パスの変更が最も一般的です。スキルファイル内で `srp_sessions` を検索し、好みのパスに書き換えてください。

## 必要環境

- **`/srp`（手動版）**: 依存なし。Claude Codeがあれば動きます。生成されるプロンプトはただのマークダウンファイルなので、コピペすればどのAIでも使えます。
- **`/quick-homo-srp`（自動版）**: [Codex CLI](https://github.com/openai/codex) が必要です。認証はサブスクリプション（ChatGPT Plus/Pro/Team/Enterprise）による自動認証、または `OPENAI_API_KEY` 環境変数のいずれかで行えます。

## 関連記事

1. **[note] [Stochastic Resonance Prompting: 初心者でも1550行→950行、50分で実現したAI協働リファクタリング手法](https://note.com/chemica_tan/n/n92f4ee61a831)**（2025年6月）— SRPの初出
2. **[Zenn] [同じプロンプトを4つのAIに投げたら、1つだけでは見えない答えが見えた — SRP実践録](https://zenn.dev/chemica_tan/articles/9e45560a8d3d6a)**（2026年2月）— 実践録

## ライセンス

[MIT](LICENSE)

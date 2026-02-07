# Stochastic Resonance Prompting (SRP)

同じプロンプトを複数のAIに投げ、確率的にゆらぐ出力を比較することで、単一AIでは見えない構造を浮かび上がらせる手法です。

物理学の**確率共鳴（Stochastic Resonance）**から着想を得ています。微弱な信号にノイズを加えると、かえって検出しやすくなる——この原理をLLMプロンプティングに応用しました。4つのAIが全員同じことを言えば強い合意、1つだけ違えば検討に値する分岐点。比較しなければ、選択肢があったこと自体に気づけません。

## スキル一覧

| スキル | 概要 | 依存環境 |
|--------|------|----------|
| `/srp` | セッションの議論からSRPプロンプトを生成し、各AIに配布するワンライナーを出力。**手動版**。 | なし（環境依存ゼロ） |
| `/quick-homo-srp` | SRPプロンプト生成 + Codex CLIで並列自動実行。**自動版**。 | [Codex CLI](https://github.com/openai/codex) |

## 導入方法

`skills/` ディレクトリの中身を、あなたのプロジェクトの `.claude/skills/` にコピーします。

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

# Waterfall スキルリポジトリ

ウォーターフォール開発で利用するスキル・エージェントを開発・管理するリポジトリ。
ここで開発したスキル・エージェントは、各開発プロジェクトの Claude Code から呼び出して使用する。

## 開発ルール（最重要）

- **スキルの新規作成には必ず `/skill-creator` を使用する**
- スキルの構成: `skills/{スキル名}/SKILL.md` + `skills/{スキル名}/references/` + `agents/{スキル名}.md`

## 参照ファイル

| ファイル | 内容 |
|---|---|
| [document-list.md](document-list.md) | 全フェーズのドキュメント一覧・命名規則・配置先。スキル開発時に入出力ドキュメントを把握するために参照する |
| [skills-list.md](skills-list.md) | 作成済みスキル一覧 |
| [sample/CLAUDE.sample.md](sample/CLAUDE.sample.md) | 開発プロジェクト向け CLAUDE.md 雛形（9フェーズワークフロー・スキル起動手順を記載） |

## リポジトリ構成

| ディレクトリ | 内容 |
|---|---|
| `skills/` | スキル定義（SKILL.md + references/）|
| `agents/` | スキルから起動されるサブエージェントプロンプト |
| `sample/` | 各プロジェクトで使用する CLAUDE.md の雛形 |

## デプロイ先

実際のプロジェクトでは以下に配置され、ディレクトリ名なしでスキル名のみで実行可能:

- `~/.claude/skills/`
- `~/.claude/agents/`

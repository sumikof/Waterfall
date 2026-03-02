# Waterfall スキルリポジトリ

各プロジェクトで利用するスキル・エージェントを管理するリポジトリ。

## リポジトリ構成

| ディレクトリ | 内容 |
|---|---|
| `skills/` | スキル定義（SKILL.md + references/）|
| `agents/` | スキルから起動されるサブエージェントプロンプト |
| `sample/` | 各プロジェクトで使用する CLAUDE.md の雛形 |

## スキル一覧

| スキル名 | ディレクトリ | 概要 |
|---|---|---|
| requirements-definition | `skills/requirements-definition/` | 要件定義フェーズ（REQ） |
| basic-design-doc | `skills/basic-design-doc/` | 基本設計フェーズ（BSD） |
| detailed-design-doc | `skills/detailed-design-doc/` | 詳細設計フェーズ（DSD） |
| implementation | `skills/implementation/` | 実装・単体テストフェーズ（IMP/TDD） |
| integration-test | `skills/integration-test/` | 結合テストフェーズ（IT） |
| system-test | `skills/system-test/` | システムテストフェーズ（ST） |
| acceptance-test | `skills/acceptance-test/` | 受入テストフェーズ（UAT） |
| unit-test | `skills/unit-test/` | TDD 方法論リファレンス |

## デプロイについて

リポジトリ内のスキル・エージェントは、実際に各プロジェクトで使用する際はプロファイル内の以下のパスに配置される。そのため、`skills/` や `agents/` などのディレクトリ名を省いたスキル名で実行可能。

- `~/.claude/skills/`
- `~/.claude/agents/`

## 開発ルール

### スキルを作成・修正する場合

**スキルの新規作成には必ず `skill-creator` スキルを使用する。**

```
/skill-creator
```

スキルの構成:
- `skills/{スキル名}/SKILL.md` — スキル本体（ワークフロー・エージェント起動手順）
- `skills/{スキル名}/references/` — スキルが参照するテンプレート・ガイド
- `agents/{スキル名}.md` — スキルから起動されるサブエージェントプロンプト

## sample/ ディレクトリ

`sample/CLAUDE.sample.md` は、このリポジトリのスキルを使用する開発プロジェクト向けの CLAUDE.md 雛形。各プロジェクトの CLAUDE.md として採用する際は、プロジェクトに合わせて適宜カスタマイズする。

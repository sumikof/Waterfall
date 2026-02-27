# Waterfall Spec-Driven Development Framework

Web システム向けのウォーターフォール + スペック駆動開発（Spec-Driven Development）+ テスト駆動開発（TDD）フレームワーク。

## 開発ワークフロー

以下の 7 フェーズを順番に実行する。各フェーズは対応する**スキルを使用して**進める。

| # | フェーズ | スキル | 主な成果物 |
|---|---|---|---|
| 1 | **REQ** 要件定義 | `skills/requirements-definition/SKILL.md` | REQ-001〜REQ-008 |
| 2 | **BSD** 基本設計 | `skills/basic-design-doc/SKILL.md` | BSD-001〜BSD-010 |
| 3 | **DSD** 詳細設計 | `skills/detailed-design-doc/SKILL.md` | DSD-001〜DSD-009（FEAT 単位） |
| 4 | **IMP** 実装（TDD） | `skills/implementation/SKILL.md` | IMP-001〜IMP-005 + ソースコード |
| 5 | **IT** 結合テスト | `skills/integration-test/SKILL.md` | IT-001〜IT-003 |
| 6 | **ST** システムテスト | `skills/system-test/SKILL.md` | ST-001〜ST-004 |
| 7 | **UAT** 受入テスト | `skills/acceptance-test/SKILL.md` | UAT-001〜UAT-003 |

> 単体テスト（UT）フェーズは存在しない。単体テストは IMP フェーズの TDD サイクル（Red→Green→Refactor）に統合されている。

## フェーズ実行方法

各フェーズを実行するときは、**対応するスキルの SKILL.md を読み込み**、スキルの「エージェント起動」セクションに従ってサブエージェントを起動する。

### スキル一覧とパラメータ

| フェーズ | スキル | パラメータ |
|---|---|---|
| REQ | `skills/requirements-definition/SKILL.md` | `{{PROJECT_ID}}`, `{{PROJECT_NAME}}` |
| BSD | `skills/basic-design-doc/SKILL.md` | `{{PROJECT_ID}}`, `{{PROJECT_NAME}}` |
| DSD | `skills/detailed-design-doc/SKILL.md` | `{{PROJECT_ID}}`, `{{PROJECT_NAME}}`, `{{FEAT_ID}}`, `{{FEAT_NAME}}` |
| IMP | `skills/implementation/SKILL.md` | `{{PROJECT_ID}}`, `{{PROJECT_NAME}}`, `{{FEAT_ID}}`, `{{FEAT_NAME}}` |
| IT | `skills/integration-test/SKILL.md` | `{{PROJECT_ID}}`, `{{PROJECT_NAME}}`, `{{FEAT_ID}}`, `{{FEAT_NAME}}` |
| ST | `skills/system-test/SKILL.md` | `{{PROJECT_ID}}`, `{{PROJECT_NAME}}` |
| UAT | `skills/acceptance-test/SKILL.md` | `{{PROJECT_ID}}`, `{{PROJECT_NAME}}` |

### 起動手順

1. `skills/{スキル名}/SKILL.md` を Read で読み込む
2. SKILL.md の「エージェント起動」セクションの指示に従い、サブエージェントプロンプト（`agents/*.md`）を Read で読み込む
3. パラメータ（`{{PROJECT_ID}}` 等）を実際の値に置換してプロンプトとする
4. Task サブエージェントを起動し、置換済みプロンプトを渡す
5. サブエージェントはスキルのワークフロー・テンプレート・入力ドキュメントを自律的に読み込んで作業する

```
起動例（BSD フェーズ）:
  1. skills/basic-design-doc/SKILL.md を Read で読み込む
  2. SKILL.md の「エージェント起動」に従い agents/basic-design-doc.md を Read で読み込む
  3. {{PROJECT_ID}} → PRJ-001, {{PROJECT_NAME}} → initial-build に置換する
  4. Task サブエージェント（general-purpose）を起動し、置換済みプロンプトを渡す

起動例（DSD フェーズ・FEAT 単位）:
  1. skills/detailed-design-doc/SKILL.md を Read で読み込む
  2. SKILL.md の「エージェント起動」に従い agents/detailed-design-doc.md を Read で読み込む
  3. {{PROJECT_ID}} → PRJ-001, {{PROJECT_NAME}} → initial-build,
     {{FEAT_ID}} → FEAT-001, {{FEAT_NAME}} → user-auth に置換する
  4. Task サブエージェントを起動し、置換済みプロンプトを渡す
  ※ 複数 FEAT は別々のサブエージェントで並行して進められる

起動例（UAT フェーズ・サブエージェント内包型）:
  1. skills/acceptance-test/SKILL.md を Read で読み込む
  2. SKILL.md の「エージェント起動」に従い agents/acceptance-test.md を Read で読み込む
  3. パラメータを置換して Task サブエージェントを起動する
  ※ UAT オーケストレーターは内部でさらにサブエージェント（コード探索・シナリオテスト）を起動する
```

## 変更管理ルール（最重要）

**ユーザーから仕様の追加・変更が発生した場合、必ず REQ（要件定義）フェーズからやり直す。**

1. 要件定義スキルを使用して要件定義書を更新する
2. 更新された REQ ドキュメントを入力として基本設計スキルを使用して BSD フェーズを再実行する
3. BSD → DSD → IMP → IT → ST → UAT の順に各スキルを使用してすべての下流フェーズのドキュメントをカスケード更新する

変更の影響範囲が限定的でも、REQ からの再実行を省略しない。これによりドキュメント間の整合性を保証する。

## ドキュメント構成（2 系統）

| ディレクトリ | 役割 | 内容 |
|---|---|---|
| `docs/` | マスタドキュメント（システム仕様の正本） | REQ-005〜008、BSD-001〜010、DSD 全体、OPS |
| `projects/PRJ-{NNN}_{名称}/` | プロジェクト固有ドキュメント | REQ-001〜004、BSD-008、IMP、IT、ST、UAT、REL |

詳細なディレクトリ構成・ファイル命名規則・ドキュメント ID 体系は `document-list.md` を正とする。

## 主要規約

### FEAT-ID によるトレーサビリティ
- REQ-005（機能一覧）で定義した `FEAT-{NNN}` を DSD 以降の全フェーズで使用する
- ファイル名: `{フェーズ}-{種別番号}_{FEAT-ID}_{機能名}.md`（例: `DSD-001_FEAT-001_user-auth.md`）
- 機能名は英語・ケバブケース（kebab-case）

### ドキュメント ID 体系
`REQ`, `BSD`, `DSD`, `IMP`, `IT`, `ST`, `UAT`, `REL`, `OPS` の各プレフィックスに連番を付与する。

### TDD 統合方針
- DSD-008（単体テスト設計書）を起点に Red→Green→Refactor サイクルで実装する
- TDD の方法論（テストの書き方・テストダブル・FIRST 原則）は単体テストスキル（`skills/unit-test/`）を参照する
- 実装スキルは単体テストスキルと連携して作業する
- テストコードと結果は IMP-001/IMP-002 に含める
- UT フォルダ・UT フェーズは使用しない

## 参照ドキュメント

- ドキュメント一覧（正本）: `document-list.md`
- 各スキルの詳細: `skills/{スキル名}/SKILL.md`
- サブエージェントプロンプト: `agents/*.md`（スキルから起動される）
- テンプレート・ガイド: `skills/{スキル名}/references/`

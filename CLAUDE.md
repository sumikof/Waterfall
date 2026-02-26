# Waterfall Spec-Driven Development Framework

Web システム向けのウォーターフォール + スペック駆動開発（Spec-Driven Development）+ テスト駆動開発（TDD）フレームワーク。

## 開発ワークフロー

以下の 7 フェーズを順番に実行する。各フェーズは対応するスキルの SKILL.md を読み込んだ Task サブエージェントで実施する。

| # | フェーズ | スキルディレクトリ | 主な成果物 |
|---|---|---|---|
| 1 | **REQ** 要件定義 | `skills/requirements-definition/` | REQ-001〜REQ-008 |
| 2 | **BSD** 基本設計 | `skills/basic-design-doc/` | BSD-001〜BSD-010 |
| 3 | **DSD** 詳細設計 | `skills/detailed-design-doc/` | DSD-001〜DSD-009（FEAT 単位） |
| 4 | **IMP** 実装（TDD） | `skills/implementation/` + `skills/unit-test/` | IMP-001〜IMP-005 + ソースコード |
| 5 | **IT** 結合テスト | `skills/integration-test/` | IT-001〜IT-003 |
| 6 | **ST** システムテスト | `skills/system-test/` | ST-001〜ST-004 |
| 7 | **UAT** 受入テスト | `skills/acceptance-test/` | UAT-001〜UAT-003 |

> 単体テスト（UT）フェーズは存在しない。単体テストは IMP フェーズの TDD サイクル（Red→Green→Refactor）に統合されている。

## フェーズ実行方法

各フェーズを実行するときは、`agents/` 配下のプロンプトファイルを読み込み、パラメータを置換して Task サブエージェントを起動する。

### サブエージェントプロンプト一覧

| フェーズ | プロンプトファイル | パラメータ |
|---|---|---|
| REQ | `agents/requirements-definition.md` | `{{PROJECT_ID}}`, `{{PROJECT_NAME}}` |
| BSD | `agents/basic-design-doc.md` | `{{PROJECT_ID}}`, `{{PROJECT_NAME}}` |
| DSD | `agents/detailed-design-doc.md` | `{{PROJECT_ID}}`, `{{PROJECT_NAME}}`, `{{FEAT_ID}}`, `{{FEAT_NAME}}` |
| IMP | `agents/implementation.md` | `{{PROJECT_ID}}`, `{{PROJECT_NAME}}`, `{{FEAT_ID}}`, `{{FEAT_NAME}}` |
| IT | `agents/integration-test.md` | `{{PROJECT_ID}}`, `{{PROJECT_NAME}}`, `{{FEAT_ID}}`, `{{FEAT_NAME}}` |
| ST | `agents/system-test.md` | `{{PROJECT_ID}}`, `{{PROJECT_NAME}}` |
| UAT | `agents/acceptance-test.md` | `{{PROJECT_ID}}`, `{{PROJECT_NAME}}` |

### 起動手順

1. `agents/{フェーズ名}.md` を Read で読み込む
2. パラメータ（`{{PROJECT_ID}}` 等）を実際の値に置換してプロンプトとする
3. Task サブエージェント（`subagent_type: "general-purpose"`）を起動し、置換済みプロンプトを渡す
4. サブエージェントは SKILL.md・テンプレート・入力ドキュメントを自律的に読み込んで作業する

```
起動例（BSD フェーズ）:
  1. agents/basic-design-doc.md を Read で読み込む
  2. {{PROJECT_ID}} → PRJ-001, {{PROJECT_NAME}} → initial-build に置換する
  3. Task サブエージェントを起動し、置換済みプロンプトを渡す

起動例（DSD フェーズ・FEAT 単位）:
  1. agents/detailed-design-doc.md を Read で読み込む
  2. {{PROJECT_ID}} → PRJ-001, {{PROJECT_NAME}} → initial-build,
     {{FEAT_ID}} → FEAT-001, {{FEAT_NAME}} → user-auth に置換する
  3. Task サブエージェントを起動し、置換済みプロンプトを渡す
  ※ 複数 FEAT は別々のサブエージェントで並行して進められる
```

## 変更管理ルール（最重要）

**ユーザーから仕様の追加・変更が発生した場合、必ず REQ（要件定義）フェーズからやり直す。**

1. REQ フェーズで要件定義書を更新する
2. 更新された REQ ドキュメントを入力として BSD フェーズを再実行する
3. BSD → DSD → IMP → IT → ST → UAT の順にすべての下流フェーズのドキュメントをカスケード更新する

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
- 実装エージェントは実装スキルと単体テストスキルの両方を読み込んで作業する
- テストコードと結果は IMP-001/IMP-002 に含める
- UT フォルダ・UT フェーズは使用しない

## 参照ドキュメント

- ドキュメント一覧（正本）: `document-list.md`
- サブエージェントプロンプト: `agents/{フェーズ名}.md`
- 各スキルの詳細: `skills/{スキル名}/SKILL.md`
- テンプレート・ガイド: `skills/{スキル名}/references/`

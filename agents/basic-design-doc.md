# BSD（基本設計）サブエージェント

## パラメータ

| パラメータ | 値 |
|---|---|
| PROJECT_ID | `{{PROJECT_ID}}` (例: `PRJ-001`) |
| PROJECT_NAME | `{{PROJECT_NAME}}` (例: `initial-build`) |

## 実行手順

### Step 1: スキル定義の読み込み

`skills/basic-design-doc/SKILL.md` を Read で読み込む。

### Step 2: 入力ドキュメントの読み込み

REQ フェーズの成果物をすべて Read で読み込む:

| ドキュメント | パス |
|---|---|
| REQ-001 | `projects/{{PROJECT_ID}}_{{PROJECT_NAME}}/REQ/REQ-001_system-requirements.md` |
| REQ-002 | `projects/{{PROJECT_ID}}_{{PROJECT_NAME}}/REQ/REQ-002_business-requirements.md` |
| REQ-003 | `projects/{{PROJECT_ID}}_{{PROJECT_NAME}}/REQ/REQ-003_use-cases.md` |
| REQ-004 | `projects/{{PROJECT_ID}}_{{PROJECT_NAME}}/REQ/REQ-004_screen-list.md` |
| REQ-005 | `docs/REQ/REQ-005_feature-list.md` |
| REQ-006 | `docs/REQ/REQ-006_non-functional-requirements.md` |
| REQ-007 | `docs/REQ/REQ-007_external-interfaces.md` |
| REQ-008 | `docs/REQ/REQ-008_glossary.md` |

ファイルが存在しない場合はユーザーに確認する。

### Step 3: ワークフロー実行

SKILL.md のワークフロー（Step 1〜5）に従い、BSD-001 → BSD-008 の順にドキュメントを生成する。

各 BSD ドキュメントの生成時には、対応するテンプレートを参照する:

| 作成対象 | テンプレート |
|---|---|
| BSD-001 | `skills/basic-design-doc/references/bsd-001-architecture.md` |
| BSD-002 | `skills/basic-design-doc/references/bsd-002-security.md` |
| BSD-003 | `skills/basic-design-doc/references/bsd-003-screen-design.md` |
| BSD-004 | `skills/basic-design-doc/references/bsd-004-business-flow.md` |
| BSD-005 | `skills/basic-design-doc/references/bsd-005-api-design.md` |
| BSD-006 | `skills/basic-design-doc/references/bsd-006-database-design.md` |
| BSD-007 | `skills/basic-design-doc/references/bsd-007-external-interface.md` |
| BSD-008 | `skills/basic-design-doc/references/bsd-008-test-plan.md` |

### Step 4: 成果物の保存

| ドキュメント | 保存先 |
|---|---|
| BSD-001 システムアーキテクチャ設計書 | `docs/BSD/BSD-001_architecture.md` |
| BSD-002 セキュリティ基本設計書 | `docs/BSD/BSD-002_security-design.md` |
| BSD-003 画面設計書 | `docs/BSD/BSD-003_screen-design.md` |
| BSD-004 業務フロー設計書 | `docs/BSD/BSD-004_business-flow.md` |
| BSD-005 API基本設計書 | `docs/BSD/BSD-005_api-design.md` |
| BSD-006 データベース基本設計書 | `docs/BSD/BSD-006_database-design.md` |
| BSD-007 外部インターフェース基本設計書 | `docs/BSD/BSD-007_external-interface-design.md` |
| BSD-008 テスト計画書 | `projects/{{PROJECT_ID}}_{{PROJECT_NAME}}/BSD/BSD-008_test-plan.md` |

保存先ディレクトリが存在しない場合は `mkdir -p` で作成してから保存する。

### Step 5: 完了報告

以下を報告する:
- 作成したドキュメント一覧（ファイルパス付き）
- 技術スタック・アーキテクチャの主要な決定事項
- DSD（詳細設計）フェーズへの申し送り事項

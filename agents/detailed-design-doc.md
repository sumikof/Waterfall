# DSD（詳細設計）サブエージェント

## パラメータ

| パラメータ | 値 |
|---|---|
| PROJECT_ID | `{{PROJECT_ID}}` (例: `PRJ-001`) |
| PROJECT_NAME | `{{PROJECT_NAME}}` (例: `initial-build`) |
| FEAT_ID | `{{FEAT_ID}}` (例: `FEAT-001`) |
| FEAT_NAME | `{{FEAT_NAME}}` (例: `user-auth`) |

## スコープ

**このサブエージェントは 1 回の起動で 1 つの FEAT-ID を担当する。**
複数の FEAT-ID は別セッションで並行して進める。

DSD-007（コーディング規約）のみシステム共通ドキュメントとして `docs/DSD/_common/` に配置する。

## 実行手順

### Step 1: スキル定義の読み込み

`skills/detailed-design-doc/SKILL.md` を Read で読み込む。

### Step 2: 入力ドキュメントの読み込み

BSD・REQ の成果物を Read で読み込む:

| ドキュメント | パス | 用途 |
|---|---|---|
| BSD-001 | `docs/BSD/BSD-001_architecture.md` | DSD-001, DSD-006 の入力 |
| BSD-002 | `docs/BSD/BSD-002_security-design.md` | DSD-001 の入力 |
| BSD-003 | `docs/BSD/BSD-003_screen-design.md` | DSD-002 の入力 |
| BSD-004 | `docs/BSD/BSD-004_business-flow.md` | DSD-001, DSD-002, DSD-006 の入力 |
| BSD-005 | `docs/BSD/BSD-005_api-design.md` | DSD-003 の入力 |
| BSD-006 | `docs/BSD/BSD-006_database-design.md` | DSD-004 の入力 |
| BSD-007 | `docs/BSD/BSD-007_external-interface-design.md` | DSD-005 の入力 |
| REQ-005 | `docs/REQ/REQ-005_feature-list.md` | 全 DSD の入力（対象機能の確認） |
| REQ-008 | `docs/REQ/REQ-008_glossary.md` | 用語統一（存在する場合） |

**重要**: DSD-008（単体テスト設計書）の入力元は BSD ではなく **同機能の DSD-001〜DSD-003**。DSD-001〜003 を先に作成してから DSD-008 を生成する。

### Step 3: ワークフロー実行

SKILL.md のワークフロー（Step 1〜5）に従い、DSD-001 → DSD-008 の順にドキュメントを生成する。

各 DSD ドキュメントの生成時には、対応するテンプレートを参照する:

| 作成対象 | テンプレート |
|---|---|
| DSD-001 | `skills/detailed-design-doc/references/dsd-001-backend.md` |
| DSD-002 | `skills/detailed-design-doc/references/dsd-002-frontend.md` |
| DSD-003 | `skills/detailed-design-doc/references/dsd-003-api.md` |
| DSD-004 | `skills/detailed-design-doc/references/dsd-004-database.md` |
| DSD-005 | `skills/detailed-design-doc/references/dsd-005-external-if.md` |
| DSD-006 | `skills/detailed-design-doc/references/dsd-006-batch.md` |
| DSD-007 | `skills/detailed-design-doc/references/dsd-007-coding-guidelines.md` |
| DSD-008 | `skills/detailed-design-doc/references/dsd-008-unit-test.md` |

DSD-005（外部IF）・DSD-006（バッチ）は該当機能でのみ作成する。

### Step 4: 成果物の保存

| ドキュメント | 保存先 |
|---|---|
| DSD-001 バックエンド機能詳細設計書 | `docs/DSD/{{FEAT_ID}}_{{FEAT_NAME}}/DSD-001_{{FEAT_ID}}_{{FEAT_NAME}}.md` |
| DSD-002 フロントエンド詳細設計書 | `docs/DSD/{{FEAT_ID}}_{{FEAT_NAME}}/DSD-002_{{FEAT_ID}}_{{FEAT_NAME}}.md` |
| DSD-003 API詳細設計書 | `docs/DSD/{{FEAT_ID}}_{{FEAT_NAME}}/DSD-003_{{FEAT_ID}}_{{FEAT_NAME}}.md` |
| DSD-004 データベース詳細設計書 | `docs/DSD/{{FEAT_ID}}_{{FEAT_NAME}}/DSD-004_{{FEAT_ID}}_{{FEAT_NAME}}.md` |
| DSD-005 外部IF詳細設計書（該当時のみ） | `docs/DSD/{{FEAT_ID}}_{{FEAT_NAME}}/DSD-005_{{FEAT_ID}}_{{FEAT_NAME}}.md` |
| DSD-006 バッチ処理詳細設計書（該当時のみ） | `docs/DSD/{{FEAT_ID}}_{{FEAT_NAME}}/DSD-006_{{FEAT_ID}}_{{FEAT_NAME}}.md` |
| DSD-007 コーディング規約（システム共通・初回のみ） | `docs/DSD/_common/DSD-007_coding-guidelines.md` |
| DSD-008 単体テスト設計書 | `docs/DSD/{{FEAT_ID}}_{{FEAT_NAME}}/DSD-008_{{FEAT_ID}}_{{FEAT_NAME}}.md` |

保存先ディレクトリが存在しない場合は `mkdir -p` で作成してから保存する。

### Step 5: 完了報告

以下を報告する:
- 作成したドキュメント一覧（ファイルパス付き）
- DSD-008 のテストケース数
- IMP（実装・TDD）フェーズへの申し送り事項

# UAT（受入テスト）サブエージェント

## パラメータ

| パラメータ | 値 |
|---|---|
| PROJECT_ID | `{{PROJECT_ID}}` (例: `PRJ-001`) |
| PROJECT_NAME | `{{PROJECT_NAME}}` (例: `initial-build`) |

## スコープ

**プロジェクト全体で 1 回実施する。** ST-001 が PASS した状態で起動する。

テスト実施順序: UAT-001 → UAT-002（随時更新）→ UAT-003（リリース可否判断）。

## 実行手順

### Step 1: スキル定義の読み込み

`skills/acceptance-test/SKILL.md` を Read で読み込む。

### Step 2: 入力ドキュメントの読み込み

**最初に読む（スコープ把握・受入基準確認）**:

| ドキュメント | パス |
|---|---|
| BSD-008 テスト計画 | `projects/{{PROJECT_ID}}_{{PROJECT_NAME}}/BSD/BSD-008_test-plan.md` |
| ST-001 システムテスト結果 | `projects/{{PROJECT_ID}}_{{PROJECT_NAME}}/ST/ST-001_system-test.md` |
| ST-004 不具合管理票 | `projects/{{PROJECT_ID}}_{{PROJECT_NAME}}/ST/ST-004_defect-list.md` |

**UAT-001（受入テスト仕様書）設計時に参照する**:

| ドキュメント | パス |
|---|---|
| REQ-001 システム要件定義書 | `projects/{{PROJECT_ID}}_{{PROJECT_NAME}}/REQ/REQ-001_system-requirements.md` |
| REQ-002 業務要件定義書 | `projects/{{PROJECT_ID}}_{{PROJECT_NAME}}/REQ/REQ-002_business-requirements.md` |
| REQ-003 ユースケース | `projects/{{PROJECT_ID}}_{{PROJECT_NAME}}/REQ/REQ-003_use-cases.md` |

> ST-001 の「UAT フェーズへの申し送り事項」と REQ-002 の業務ルールを必ず確認してからシナリオ設計を開始する。

### Step 3: ワークフロー実行

SKILL.md のワークフローに従い、UAT-001 → UAT-002 → UAT-003 の順に作業する。テンプレートは `skills/acceptance-test/references/uat-templates.md` を参照する。

**UAT-001**: 受入シナリオ設計（UTC-{NNN}）→ テスト仕様設計 → テスト実施 → 完了判定
**UAT-002**: 不具合管理（BUG-{NNN}、ST-004 の通番から継続）
**UAT-003**: テスト結果サマリー → 不具合サマリー → リリース可否判断（PASS / CONDITIONAL PASS / FAIL）

### Step 4: 成果物の保存

| ドキュメント | 保存先 |
|---|---|
| UAT-001 受入テスト仕様・結果書 | `projects/{{PROJECT_ID}}_{{PROJECT_NAME}}/UAT/UAT-001_acceptance-test.md` |
| UAT-002 不具合管理票 | `projects/{{PROJECT_ID}}_{{PROJECT_NAME}}/UAT/UAT-002_defect-list.md` |
| UAT-003 受入完了報告書 | `projects/{{PROJECT_ID}}_{{PROJECT_NAME}}/UAT/UAT-003_acceptance-report.md` |

保存先ディレクトリが存在しない場合は `mkdir -p` で作成してから保存する。

### Step 5: 完了報告

以下を報告する:
- 作成したドキュメント一覧（ファイルパス付き）
- テスト結果サマリー（OK / NG / SKIP 件数）
- リリース可否判断（PASS / CONDITIONAL PASS / FAIL）
- 不具合一覧（UAT-002 に登録した BUG-ID）
- REL（リリース）フェーズへの申し送り事項

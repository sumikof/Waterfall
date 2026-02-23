# ST（システムテスト）サブエージェント

## パラメータ

| パラメータ | 値 |
|---|---|
| PROJECT_ID | `{{PROJECT_ID}}` (例: `PRJ-001`) |
| PROJECT_NAME | `{{PROJECT_NAME}}` (例: `initial-build`) |

## スコープ

**プロジェクト全体で 1 回実施する。** 全 IT-001 が完了し、IT-002・IT-003 が揃った状態で起動する。

テスト実施順序: ST-001 → ST-002 → ST-003（ST-002 と ST-003 は並行可）。ST-004 は全テスト中随時追記する。

## 実行手順

### Step 1: スキル定義の読み込み

`skills/system-test/SKILL.md` を Read で読み込む。

### Step 2: 入力ドキュメントの読み込み

**最初に読む（スコープ把握・方針確認）**:

| ドキュメント | パス |
|---|---|
| BSD-008 テスト計画 | `projects/{{PROJECT_ID}}_{{PROJECT_NAME}}/BSD/BSD-008_test-plan.md` |
| IT-003 不具合管理票 | `projects/{{PROJECT_ID}}_{{PROJECT_NAME}}/IT/IT-003_defect-list.md` |
| REQ-003 ユースケース | `projects/{{PROJECT_ID}}_{{PROJECT_NAME}}/REQ/REQ-003_use-cases.md` |
| REQ-005 機能一覧 | `docs/REQ/REQ-005_feature-list.md` |

**ST-001（システムテスト）設計時に参照する**:

| ドキュメント | パス |
|---|---|
| 全 IT-001 | `projects/{{PROJECT_ID}}_{{PROJECT_NAME}}/IT/FEAT-{NNN}_{機能名}/IT-001_FEAT-{NNN}_{機能名}.md` |

**ST-002（性能テスト）設計時に追加参照する**:

| ドキュメント | パス |
|---|---|
| REQ-006 非機能要件 | `docs/REQ/REQ-006_non-functional-requirements.md` |

**ST-003（セキュリティテスト）設計時に追加参照する**:

| ドキュメント | パス |
|---|---|
| REQ-006 非機能要件 | `docs/REQ/REQ-006_non-functional-requirements.md` |
| BSD-002 セキュリティ基本設計 | `docs/BSD/BSD-002_security-design.md` |

> IT-001 の「ST フェーズへの申し送り事項」を必ず確認してからテスト設計を開始する。

### Step 3: ワークフロー実行

SKILL.md のワークフローに従い、ST-001 → ST-002 → ST-003 の順にテストを実施する。テンプレートは `skills/system-test/references/st-templates.md` を参照する。

**ST-001**: E2E シナリオ設計（STC-{NNN}）→ テスト仕様設計 → テスト実施 → 完了判定
**ST-002**: 性能テスト設計（PTC-{NNN}）→ 負荷テスト実施 → 性能要件充足判定
**ST-003**: 脆弱性診断（SEC-{NNN}）→ 認証・認可テスト → 暗号化確認

不具合 ID は IT-003 の BUG-NNN の続きから採番する。

### Step 4: 成果物の保存

| ドキュメント | 保存先 |
|---|---|
| ST-001 システムテスト仕様・結果書 | `projects/{{PROJECT_ID}}_{{PROJECT_NAME}}/ST/ST-001_system-test.md` |
| ST-002 性能テスト仕様・結果書 | `projects/{{PROJECT_ID}}_{{PROJECT_NAME}}/ST/ST-002_performance-test.md` |
| ST-003 セキュリティテスト仕様・結果書 | `projects/{{PROJECT_ID}}_{{PROJECT_NAME}}/ST/ST-003_security-test.md` |
| ST-004 不具合管理票 | `projects/{{PROJECT_ID}}_{{PROJECT_NAME}}/ST/ST-004_defect-list.md` |

保存先ディレクトリが存在しない場合は `mkdir -p` で作成してから保存する。

### Step 5: 完了報告

以下を報告する:
- 作成したドキュメント一覧（ファイルパス付き）
- テスト結果サマリー（ST-001 / ST-002 / ST-003 各々の OK / NG / SKIP 件数）
- 不具合一覧（ST-004 に登録した BUG-ID）
- UAT（受入テスト）フェーズへの申し送り事項

# IT（結合テスト）実行エージェント

> このプロンプトは結合テストスキル（`skills/integration-test/SKILL.md`）から起動されるサブエージェントである。

## パラメータ

| パラメータ | 値 |
|---|---|
| PROJECT_ID | `{{PROJECT_ID}}` (例: `PRJ-001`) |
| PROJECT_NAME | `{{PROJECT_NAME}}` (例: `initial-build`) |
| FEAT_ID | `{{FEAT_ID}}` (例: `FEAT-001`。IT-002 実行時は空欄) |
| FEAT_NAME | `{{FEAT_NAME}}` (例: `user-auth`。IT-002 実行時は空欄) |

## スコープ

**2 つの起動モードがある:**

| モード | FEAT_ID | 作成対象 | 実行条件 |
|---|---|---|---|
| **FEAT 単位モード** | 指定あり | IT-001_{FEAT-ID}（+ IT-003 追記） | 対象 FEAT の IMP 完了後 |
| **横断モード** | 空欄 | IT-002（+ IT-003 追記） | 全 IT-001 完了後 |

- IT-001 は 1 回の起動で 1 つの FEAT-ID を担当する。複数の FEAT-ID は別セッションで並行して進める
- IT-002 は全 IT-001 が完了した後に横断モードで実施する
- IT-003 は全 FEAT セッションで共有するファイル。不具合発見時に随時追記する

## 実行手順

### Step 1: スキル・入力ドキュメントの読み込み

`skills/integration-test/SKILL.md` を Read で読み込む。

続けて、入力ドキュメントを読み込む。

#### FEAT 単位モード（IT-001）

**最初に読む（スコープ把握）**:

| ドキュメント | パス |
|---|---|
| IMP-001 BE実装完了報告書 | `projects/{{PROJECT_ID}}_{{PROJECT_NAME}}/IMP/{{FEAT_ID}}_{{FEAT_NAME}}/IMP-001_{{FEAT_ID}}_{{FEAT_NAME}}.md` |
| IMP-002 FE実装完了報告書 | `projects/{{PROJECT_ID}}_{{PROJECT_NAME}}/IMP/{{FEAT_ID}}_{{FEAT_NAME}}/IMP-002_{{FEAT_ID}}_{{FEAT_NAME}}.md` |
| BSD-008 テスト計画 | `projects/{{PROJECT_ID}}_{{PROJECT_NAME}}/BSD/BSD-008_test-plan.md` |

**テスト設計時に参照する**:

| ドキュメント | パス |
|---|---|
| DSD-003 API詳細設計 | `docs/DSD/{{FEAT_ID}}_{{FEAT_NAME}}/DSD-003_{{FEAT_ID}}_{{FEAT_NAME}}.md` |
| DSD-005 外部IF詳細設計（該当時） | `docs/DSD/{{FEAT_ID}}_{{FEAT_NAME}}/DSD-005_{{FEAT_ID}}_{{FEAT_NAME}}.md` |

> IMP の「申し送り事項」セクションを必ず確認してからテスト設計を開始する。

#### 横断モード（IT-002）

全 FEAT-ID の DSD-003 と IT-001 を参照し、全 API エンドポイントを一覧化する:

| ドキュメント | パス |
|---|---|
| 全 IT-001 | `projects/{{PROJECT_ID}}_{{PROJECT_NAME}}/IT/FEAT-{NNN}_{機能名}/IT-001_FEAT-{NNN}_{機能名}.md` |
| 全 DSD-003 | `docs/DSD/FEAT-{NNN}_{機能名}/DSD-003_FEAT-{NNN}_{機能名}.md` |

### Step 2: ワークフロー実行

SKILL.md のテストワークフローに従い作業する。テンプレートは `skills/integration-test/references/it-templates.md` を参照する。

**FEAT 単位モード**: テストシナリオリスト作成 → テスト仕様設計 → テスト実施 → 完了判定
**横断モード**: 全エンドポイント横断整理 → クロス機能テスト実施 → エンドポイント網羅確認

### Step 3: 成果物の保存

#### FEAT 単位モード

| ドキュメント | 保存先 |
|---|---|
| IT-001 結合テスト仕様・結果書 | `projects/{{PROJECT_ID}}_{{PROJECT_NAME}}/IT/{{FEAT_ID}}_{{FEAT_NAME}}/IT-001_{{FEAT_ID}}_{{FEAT_NAME}}.md` |
| IT-003 不具合管理票（追記） | `projects/{{PROJECT_ID}}_{{PROJECT_NAME}}/IT/IT-003_defect-list.md` |

#### 横断モード

| ドキュメント | 保存先 |
|---|---|
| IT-002 API結合テスト仕様・結果書 | `projects/{{PROJECT_ID}}_{{PROJECT_NAME}}/IT/IT-002_api-integration-test.md` |
| IT-003 不具合管理票（追記） | `projects/{{PROJECT_ID}}_{{PROJECT_NAME}}/IT/IT-003_defect-list.md` |

保存先ディレクトリが存在しない場合は `mkdir -p` で作成してから保存する。

### Step 4: 完了報告

以下を報告する:
- 作成したドキュメント一覧（ファイルパス付き）
- テストケース数と結果サマリー（OK / NG / SKIP）
- 不具合一覧（IT-003 に追記した BUG-ID）
- ST（システムテスト）フェーズへの申し送り事項

# REQ（要件定義）サブエージェント

## パラメータ

| パラメータ | 値 |
|---|---|
| PROJECT_ID | `{{PROJECT_ID}}` (例: `PRJ-001`) |
| PROJECT_NAME | `{{PROJECT_NAME}}` (例: `initial-build`) |

## 実行手順

### Step 1: スキル定義の読み込み

`skills/requirements-definition/SKILL.md` を Read で読み込む。

### Step 2: 参照ドキュメントの読み込み

以下のファイルを Read で読み込む:

| ファイル | 用途 |
|---|---|
| `skills/requirements-definition/references/interview-guide.md` | ヒアリング質問集 |
| `skills/requirements-definition/references/document-templates.md` | ドキュメントテンプレート |
| `document-list.md` | 配置ルール・ドキュメント定義 |

### Step 3: ワークフロー実行

SKILL.md のワークフロー（フェーズ 1〜8）に従い、ユーザーへのヒアリングを繰り返しながら要件定義書を作成する。

**ヒアリングの進め方**:
- 一度に多くを聞かない（1 回のやり取りで 2〜3 問まで）
- ユーザーの発言の用語をそのまま使い、ユビキタス言語として記録する
- 各フェーズの終わりに内容を要約してユーザーに確認する
- 不明点は掘り下げる

### Step 4: 成果物の保存

| ドキュメント | 保存先 |
|---|---|
| REQ-001 システム要件定義書 | `projects/{{PROJECT_ID}}_{{PROJECT_NAME}}/REQ/REQ-001_system-requirements.md` |
| REQ-002 業務要件定義書 | `projects/{{PROJECT_ID}}_{{PROJECT_NAME}}/REQ/REQ-002_business-requirements.md` |
| REQ-003 ユースケース一覧・仕様書 | `projects/{{PROJECT_ID}}_{{PROJECT_NAME}}/REQ/REQ-003_use-cases.md` |
| REQ-004 画面一覧 | `projects/{{PROJECT_ID}}_{{PROJECT_NAME}}/REQ/REQ-004_screen-list.md` |
| REQ-005 機能一覧 | `docs/REQ/REQ-005_feature-list.md` |
| REQ-006 非機能要件定義書 | `docs/REQ/REQ-006_non-functional-requirements.md` |
| REQ-007 外部システム・インターフェース一覧 | `docs/REQ/REQ-007_external-interfaces.md` |
| REQ-008 用語定義書 | `docs/REQ/REQ-008_glossary.md` |

保存先ディレクトリが存在しない場合は `mkdir -p` で作成してから保存する。

### Step 5: 完了報告

以下を報告する:
- 作成したドキュメント一覧（ファイルパス付き）
- REQ-005 で定義した FEAT-ID の一覧
- BSD（基本設計）フェーズへの申し送り事項

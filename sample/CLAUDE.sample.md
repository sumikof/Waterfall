# Waterfall Spec-Driven Development Framework

Web システム向けのウォーターフォール + スペック駆動開発（Spec-Driven Development）+ テスト駆動開発（TDD）フレームワーク。

## 開発ワークフロー

以下の 9 フェーズを順番に実行する。フェーズ 1〜7 は対応する**スキルを使用して**進める。

| # | フェーズ | スキル | 主な成果物 |
|---|---|---|---|
| 1 | **REQ** 要件定義 | `skills/requirements-definition/SKILL.md` | REQ-001〜REQ-008 |
| 2 | **BSD** 基本設計 | `skills/basic-design-doc/SKILL.md` | BSD-001〜BSD-010 |
| 3 | **DSD** 詳細設計 | `skills/detailed-design-doc/SKILL.md` | DSD-001〜DSD-009（FEAT 単位） |
| 4 | **IMP** 実装（TDD） | `skills/implementation/SKILL.md` | IMP-001〜IMP-005 + ソースコード |
| 5 | **IT** 結合テスト | `skills/integration-test/SKILL.md` | IT-001〜IT-003 |
| 6 | **ST** システムテスト | `skills/system-test/SKILL.md` | ST-001〜ST-004 |
| 7 | **UAT** 受入テスト | `skills/acceptance-test/SKILL.md` | UAT-001〜UAT-003 |
| 8 | **REL** リリース | （手動） | REL-001〜REL-003 |
| 9 | **OPS** 運用・保守 | （手動） | OPS-001〜OPS-004 |

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

---

## ドキュメント ID 体系

| プレフィックス | フェーズ | 例 |
|---|---|---|
| `REQ` | 要件定義 | REQ-001 |
| `BSD` | 基本設計 | BSD-001 |
| `DSD` | 詳細設計 | DSD-001 |
| `IMP` | 実装・単体テスト（TDD） | IMP-001 |
| `IT` | 結合テスト | IT-001 |
| `ST` | システムテスト | ST-001 |
| `UAT` | 受入テスト | UAT-001 |
| `REL` | リリース | REL-001 |
| `OPS` | 運用・保守 | OPS-001 |
| `FEAT` | 機能識別子（REQ-005 で定義） | FEAT-001 |
| `PRJ` | プロジェクト識別子 | PRJ-001 |

## 機能別ドキュメント命名規則

詳細設計（DSD）フェーズ以降の**機能別**ドキュメントは、REQ-005（機能一覧）で定義した**機能 ID（`FEAT-{NNN}`）**をドキュメント ID およびファイル名に含めることで、工程をまたいだ追跡を可能にする。

### 命名形式

| 要素 | 形式 | 例 |
|---|---|---|
| 機能 ID | `FEAT-{NNN}` | `FEAT-001` |
| ドキュメント ID | `{フェーズ}-{種別番号}_{FEAT-ID}` | `DSD-001_FEAT-001` |
| ファイル名 | `{フェーズ}-{種別番号}_{FEAT-ID}_{機能名称}.md` | `DSD-001_FEAT-001_user-auth.md` |

> **機能名称**は REQ-005 の機能名を英語・ケバブケース（kebab-case）で表記する。

### 作成単位の区分

| 区分 | 記法 | 意味 |
|---|---|---|
| **機能別** | `{フェーズ}-{種別番号}_{FEAT-ID}` | REQ-005 の機能ごとに 1 ファイル作成。ファイル名に `FEAT-{NNN}` を含む |
| **システム共通** | `{フェーズ}-{種別番号}` | プロジェクト全体で 1 ファイル作成。従来の命名規則（機能 ID なし） |

### 工程間トレース例

同一の機能 ID（`FEAT-NNN`）を用いることで、詳細設計 → 実装 → テストを横断して追跡する。

```
# FEAT-001（例: ユーザー認証）のドキュメント群
DSD-009_FEAT-001_user-auth.md    ← ドメインモデル詳細設計書（DDD戦術設計・最初に生成）
DSD-001_FEAT-001_user-auth.md    ← バックエンド機能詳細設計書
DSD-002_FEAT-001_user-auth.md    ← フロントエンド詳細設計書
DSD-003_FEAT-001_user-auth.md    ← API詳細設計書
DSD-004_FEAT-001_user-auth.md    ← データベース詳細設計書
DSD-008_FEAT-001_user-auth.md    ← 単体テスト設計書（TDDの起点）
IMP-001_FEAT-001_user-auth.md    ← バックエンド実装・単体テスト完了報告書（TDD結果含む）
IMP-002_FEAT-001_user-auth.md    ← フロントエンド実装・単体テスト完了報告書（TDD結果含む）
IT-001_FEAT-001_user-auth.md     ← 結合テスト仕様・結果書
```

---

## ドキュメント構成（2 系統）

ドキュメントは **`docs/`（マスタドキュメント）** と **`projects/`（プロジェクト固有ドキュメント）** の 2 系統で管理する。新規構築・改修対応のどちらにも対応できる。

| ディレクトリ | 内容 | バージョン管理の考え方 |
|---|---|---|
| `docs/` | **マスタドキュメント（システム仕様の正本）**。現在のシステムの設計・仕様・運用手順を集約する | git で変更履歴を管理。プロジェクトによる更新は `docs/` を直接編集してコミットする |
| `projects/PRJ-{NNN}_{名称}/` | **プロジェクト固有ドキュメント**。プロジェクトごとに作成する要件・テスト結果・リリース資料 | プロジェクトフォルダごと git で管理 |

> `docs/` のマスタドキュメントはプロジェクトの進行に合わせて直接更新する。変更履歴は git のコミットログ・ブランチで追跡できるため、プロジェクト別コピーは不要。

### docs/ に置くマスタドキュメント

| フェーズ | 対象ドキュメント | 性質 |
|---|---|---|
| REQ | REQ-005〜REQ-008 | システム全体の機能一覧・要件・用語の正本 |
| BSD | BSD-001〜BSD-010 | 現行アーキテクチャ・設計方針の正本 |
| DSD | DSD-001〜DSD-009（機能別） + DSD-007 | 機能ごとの詳細仕様の正本 |
| OPS | OPS-001〜OPS-004 | 現行運用手順の正本 |

### projects/ に置くプロジェクト固有ドキュメント

| フェーズ | 対象ドキュメント | 備考 |
|---|---|---|
| REQ | REQ-001〜REQ-004 | このプロジェクトで何を作るか・何を変えるかの要件 |
| BSD | BSD-008 | このプロジェクトのテスト計画 |
| IMP | IMP-001〜IMP-005 | 実装・単体テスト（TDD）報告・マイグレーション。IMP-003 は初回構築・大規模環境変更時のみ |
| IT | IT-001〜IT-003 | このプロジェクトのテスト結果・不具合 |
| ST | ST-001〜ST-004 | このプロジェクトのテスト結果・不具合 |
| UAT | UAT-001〜UAT-003 | このプロジェクトの受入テスト結果 |
| REL | REL-001〜REL-003 | このプロジェクトのリリース計画・手順・結果 |

### ディレクトリツリー

```
docs/                                          # マスタドキュメント（システム仕様の正本・git管理）
├── REQ/
│   ├── REQ-005_feature-list.md               # 機能一覧（全機能のマスタ・FEAT-NNN の定義元）
│   ├── REQ-006_non-functional-requirements.md
│   ├── REQ-007_external-interfaces.md
│   └── REQ-008_glossary.md
├── BSD/
│   ├── BSD-001_architecture.md
│   ├── BSD-002_security-design.md
│   ├── BSD-003_screen-design.md
│   ├── BSD-004_business-flow.md
│   ├── BSD-005_api-design.md
│   ├── BSD-006_database-design.md
│   ├── BSD-007_external-interface-design.md
│   ├── BSD-009_domain-model.md
│   └── BSD-010_data-architecture.md
├── DSD/
│   ├── _common/
│   │   └── DSD-007_coding-guidelines.md
│   ├── FEAT-001_{機能名}/
│   │   └── ...（DSD-001〜DSD-008）
│   └── FEAT-NNN_{機能名}/
│       └── ...
└── OPS/
    ├── OPS-001_operations-manual.md
    ├── OPS-002_incident-response.md
    ├── OPS-003_change-management.md
    └── OPS-004_backup-restore.md

projects/                                      # プロジェクト固有ドキュメント
├── PRJ-001_{プロジェクト名}/                   # 例: 新規構築
│   ├── project.md                            # プロジェクト概要
│   ├── REQ/                                  # プロジェクト要件定義
│   │   ├── REQ-001_system-requirements.md
│   │   ├── REQ-002_business-requirements.md
│   │   ├── REQ-003_use-cases.md
│   │   └── REQ-004_screen-list.md
│   ├── BSD/
│   │   └── BSD-008_test-plan.md              # テスト計画（プロジェクト固有）
│   ├── IMP/
│   │   ├── IMP-003_environment-setup.md      # 初回構築・大規模環境変更時のみ
│   │   ├── IMP-004_db-migration.md
│   │   ├── IMP-005_tdd-defect-list.md        # TDD不具合管理票（全機能を集約）
│   │   ├── FEAT-001_{機能名}/
│   │   │   ├── IMP-001_FEAT-001_{機能名}.md  # BE実装・単体テスト完了報告書
│   │   │   └── IMP-002_FEAT-001_{機能名}.md  # FE実装・単体テスト完了報告書
│   │   └── FEAT-NNN_{機能名}/
│   │       └── ...
│   ├── IT/
│   │   ├── IT-002_api-integration-test.md
│   │   ├── IT-003_defect-list.md
│   │   ├── FEAT-001_{機能名}/
│   │   │   └── IT-001_FEAT-001_{機能名}.md
│   │   └── FEAT-NNN_{機能名}/
│   │       └── ...
│   ├── ST/
│   │   ├── ST-001_system-test.md
│   │   ├── ST-002_performance-test.md
│   │   ├── ST-003_security-test.md
│   │   └── ST-004_defect-list.md
│   ├── UAT/
│   │   ├── UAT-001_acceptance-test.md
│   │   ├── UAT-002_defect-list.md
│   │   └── UAT-003_acceptance-report.md
│   └── REL/
│       ├── REL-001_release-plan.md
│       ├── REL-002_release-procedure.md
│       └── REL-003_release-report.md
│
└── PRJ-002_{プロジェクト名}/                   # 例: 改修プロジェクト
    ├── project.md
    ├── REQ/                                  # 変更要件のみ（差分）
    │   ├── REQ-001_change-requirements.md    # 変更・追加要件
    │   └── REQ-003_use-cases.md             # 変更対象ユースケースのみ
    ├── IMP/
    │   ├── IMP-004_db-migration.md
    │   ├── IMP-005_tdd-defect-list.md
    │   └── FEAT-003_{機能名}/
    │       └── ...
    ├── IT/
    ├── ST/
    ├── UAT/
    └── REL/
```

### フォルダ・ファイル命名規則

| フォルダ・ファイル | 命名形式 | 例 |
|---|---|---|
| プロジェクトフォルダ | `PRJ-{NNN}_{プロジェクト名}/` | `PRJ-001_initial-build/` |
| システム共通サブフォルダ | `_common/` | `_common/` |
| 機能別サブフォルダ | `FEAT-{NNN}_{機能名}/` | `FEAT-001_user-auth/` |
| プロジェクト概要ファイル | `project.md` | `project.md` |

> `_common/` の先頭アンダースコアにより、ファイルシステム上で機能別フォルダより前に表示される。

### `project.md` の記載項目

各プロジェクトフォルダ直下に `project.md` を置き、以下を記載する。

```markdown
# プロジェクト概要

| 項目 | 値 |
|---|---|
| プロジェクトID | PRJ-001 |
| プロジェクト名 | ○○機能追加 |
| 種別 | 新規構築 / 機能追加 / バグ修正 / リファクタリング |
| 目的・背景 | （記述） |
| 対象機能（FEAT-ID） | FEAT-001, FEAT-002 |
| 開始日 | YYYY-MM-DD |
| リリース予定日 | YYYY-MM-DD |
| docs/ 更新対象ファイル | docs/BSD/BSD-001, docs/DSD/FEAT-001/, docs/REQ/REQ-005, ... |
```

---

## 成果物一覧

### フェーズ 1: 要件定義 (REQ)

> **配置先**: REQ-001〜REQ-004 → `projects/PRJ-{NNN}/REQ/`、REQ-005〜REQ-008 → `docs/REQ/`

| ドキュメントID | ドキュメント名 | ファイル名 | 配置先 | 説明 | 入力元 | 後続への影響 |
|---|---|---|---|---|---|---|
| REQ-001 | システム要件定義書 | `REQ-001_system-requirements.md` | `projects/` | システム全体の機能・非機能要件 | ヒアリング資料 | BSD-001, BSD-002 |
| REQ-002 | 業務要件定義書 | `REQ-002_business-requirements.md` | `projects/` | 業務フロー・業務ルールの定義 | ヒアリング資料 | BSD-004 |
| REQ-003 | ユースケース一覧・仕様書 | `REQ-003_use-cases.md` | `projects/` | アクター別ユースケースと詳細シナリオ | REQ-001, REQ-002 | BSD-003, BSD-005 |
| REQ-004 | 画面一覧 | `REQ-004_screen-list.md` | `projects/` | 全画面の ID・名称・概要・遷移元/先 | REQ-003 | BSD-003 |
| REQ-005 | 機能一覧 | `REQ-005_feature-list.md` | `docs/REQ/` | 機能 ID（FEAT-NNN）・機能名・優先度・担当領域。詳細設計以降の機能 ID の基準 | REQ-001, REQ-003 | BSD-001, DSD 全体 |
| REQ-006 | 非機能要件定義書 | `REQ-006_non-functional-requirements.md` | `docs/REQ/` | 性能・セキュリティ・可用性・保守性要件 | REQ-001 | BSD-002, ST-001 |
| REQ-007 | 外部システム・インターフェース一覧 | `REQ-007_external-interfaces.md` | `docs/REQ/` | 連携先システム・API・データ形式の概要 | REQ-001 | BSD-006 |
| REQ-008 | 用語定義書（ユビキタス言語） | `REQ-008_glossary.md` | `docs/REQ/` | ドメイン用語・略語・定義の統一辞書 | ヒアリング資料 | 全フェーズ参照 |

### フェーズ 2: 基本設計 (BSD)

> **配置先**: BSD-001〜BSD-007, BSD-009〜BSD-010 → `docs/BSD/`、BSD-008 → `projects/PRJ-{NNN}/BSD/`

| ドキュメントID | ドキュメント名 | ファイル名 | 説明 | 入力元 | 後続への影響 |
|---|---|---|---|---|---|
| BSD-001 | システムアーキテクチャ設計書 | `BSD-001_architecture.md` | システム全体構成・技術スタック・インフラ構成図 | REQ-001, REQ-005, REQ-006 | DSD 全体, DSD-007 |
| BSD-002 | セキュリティ基本設計書 | `BSD-002_security-design.md` | 認証・認可方式・通信暗号化・脆弱性対策方針 | REQ-006 | DSD-001, DSD-003 |
| BSD-003 | 画面設計書（ワイヤーフレーム） | `BSD-003_screen-design.md` | 全画面のレイアウト・コンポーネント・画面遷移図 | REQ-003, REQ-004 | DSD-002 |
| BSD-004 | 業務フロー設計書 | `BSD-004_business-flow.md` | システムを介した業務フロー（FE・BE 連携含む） | REQ-002, REQ-003 | DSD-001, DSD-002 |
| BSD-005 | API 基本設計書 | `BSD-005_api-design.md` | エンドポイント一覧・HTTP メソッド・リクエスト/レスポンス概要・認証方式 | REQ-003, REQ-005, REQ-007 | DSD-003 |
| BSD-006 | データベース基本設計書 | `BSD-006_database-design.md` | ER 図・主要テーブル定義・データモデル方針 | REQ-005, REQ-007 | DSD-004 |
| BSD-007 | 外部インターフェース基本設計書 | `BSD-007_external-interface-design.md` | 外部システム連携方式・データフォーマット・通信プロトコル | REQ-007 | DSD-005 |
| BSD-008 | テスト計画書（基本） | `BSD-008_test-plan.md` | テスト方針・テストフェーズ構成・品質基準・環境計画。TDD 方針を記載 | REQ-006 | IMP, IT, ST, UAT |
| BSD-009 | ドメインモデル設計書（DDD 戦略設計） | `BSD-009_domain-model.md` | ユビキタス言語辞書・境界づけられたコンテキストマップ・統合パターン・サブドメイン分類 | REQ-002, REQ-003, REQ-005, REQ-008 | BSD-010, DSD-009, DSD-001, DSD-004, DSD-007 |
| BSD-010 | データアーキテクチャ設計書 | `BSD-010_data-architecture.md` | データ戦略・概念データモデル・データフロー・ライフサイクル管理・分析基盤・データ品質ガバナンス | REQ-005, REQ-006, REQ-007, BSD-006, BSD-009 | DSD-004, DSD-009, DSD-006, IMP-004, OPS-004 |

### フェーズ 3: 詳細設計 (DSD)

> **配置先**: すべて `docs/DSD/`。DSD-007 のみシステム共通、それ以外は機能別（FEAT 単位）。

| ドキュメントID | ドキュメント名 | ファイル名 | 作成単位 | 説明 | 入力元 | 後続への影響 |
|---|---|---|---|---|---|---|
| DSD-001_{FEAT-ID} | バックエンド機能詳細設計書 | `DSD-001_{FEAT-ID}_{機能名}.md` | 機能別 | モジュール分割・クラス図・シーケンス図・処理フロー・エラーハンドリング | BSD-001, BSD-002, BSD-004, REQ-005 | IMP-001 |
| DSD-002_{FEAT-ID} | フロントエンド詳細設計書 | `DSD-002_{FEAT-ID}_{機能名}.md` | 機能別 | コンポーネント構成・状態管理・ルーティング・UI ロジック | BSD-003, BSD-004, REQ-005 | IMP-002 |
| DSD-003_{FEAT-ID} | API 詳細設計書 | `DSD-003_{FEAT-ID}_{機能名}.md` | 機能別 | エンドポイント詳細仕様（リクエスト/レスポンス全項目・バリデーション・エラーコード） | BSD-005, REQ-005 | IMP-001, IMP-002, IT-001 |
| DSD-004_{FEAT-ID} | データベース詳細設計書 | `DSD-004_{FEAT-ID}_{機能名}.md` | 機能別 | テーブル定義・カラム型・制約・インデックス・マイグレーション方針 | BSD-006, REQ-005 | IMP-001, IMP-004 |
| DSD-005_{FEAT-ID} | 外部インターフェース詳細設計書 | `DSD-005_{FEAT-ID}_{機能名}.md` | 機能別（外部 IF 使用時のみ） | 外部 API 呼び出し仕様・リトライ/タイムアウト・エラー処理詳細 | BSD-007, REQ-005 | IMP-001, IT-001 |
| DSD-006_{FEAT-ID} | バッチ・非同期処理詳細設計書 | `DSD-006_{FEAT-ID}_{機能名}.md` | 機能別（バッチ/非同期使用時のみ） | バッチ処理・キュー・非同期ジョブの仕様（スケジュール・処理順序・冪等性） | BSD-001, BSD-004, REQ-005 | IMP-001 |
| DSD-007 | コーディング規約・開発ガイドライン | `DSD-007_coding-guidelines.md` | システム共通 | 言語別コーディング規約・フォルダ構成・命名規則・レビュー基準 | BSD-001 | IMP-001, IMP-002 |
| DSD-008_{FEAT-ID} | 単体テスト設計書（TDD 起点） | `DSD-008_{FEAT-ID}_{機能名}.md` | 機能別 | TDD の起点となるテストケース設計書。テスト対象関数/コンポーネント・テストケース・モック方針 | DSD-001, DSD-002, DSD-003, DSD-009 | IMP-001, IMP-002 |
| DSD-009_{FEAT-ID} | ドメインモデル詳細設計書（DDD 戦術設計） | `DSD-009_{FEAT-ID}_{機能名}.md` | 機能別 | 集約設計・エンティティ・値オブジェクト・ドメインサービス・リポジトリ IF・ドメインイベント・腐敗防止層 | BSD-009, BSD-010, REQ-005 | DSD-001, DSD-004, DSD-008, IMP-001 |

### フェーズ 4: 実装・単体テスト (IMP)

> **配置先**: すべて `projects/PRJ-{NNN}/IMP/`。IMP-003 は初回構築・大規模環境変更時のみ。
> **TDD 方針**: DSD-008 を起点に Red→Green→Refactor サイクルで実装する。テストコードと実行結果は IMP-001/002 に含める。UT フェーズは存在しない。

| ドキュメントID | ドキュメント名 | ファイル名 | 作成単位 | 説明 | 入力元 | 後続への影響 |
|---|---|---|---|---|---|---|
| IMP-001_{FEAT-ID} | バックエンド実装・単体テスト完了報告書 | `IMP-001_{FEAT-ID}_{機能名}.md` | 機能別 | 実装済み機能・TDD テストコード・テスト結果・カバレッジ・設計差異一覧・既知の制限事項 | DSD-001, DSD-003, DSD-004, DSD-006, DSD-008 | IT-001 |
| IMP-002_{FEAT-ID} | フロントエンド実装・単体テスト完了報告書 | `IMP-002_{FEAT-ID}_{機能名}.md` | 機能別 | 実装済み画面・コンポーネント・TDD テストコード・テスト結果・設計差異一覧 | DSD-002, DSD-003, DSD-008 | IT-001 |
| IMP-003 | 環境構築手順書 | `IMP-003_environment-setup.md` | プロジェクト共通 | 開発・テスト・本番環境の構築手順・設定値一覧 | BSD-001 | REL-002, OPS-001 |
| IMP-004 | DB 初期データ・マイグレーション手順書 | `IMP-004_db-migration.md` | プロジェクト共通 | 全機能のマイグレーションスクリプト一覧・実行順序・ロールバック手順 | DSD-004 | REL-002, OPS-002 |
| IMP-005 | TDD 不具合管理票 | `IMP-005_tdd-defect-list.md` | プロジェクト共通 | TDD サイクル中に発見したバグ ID・発生箇所（FEAT-ID 付）・重大度・対応状況・修正結果（全機能を集約） | IMP-001, IMP-002 | IT-001 |

### フェーズ 5: 結合テスト (IT)

> **配置先**: すべて `projects/PRJ-{NNN}/IT/`。IT-001 は機能別、IT-002・IT-003 はプロジェクト共通。

| ドキュメントID | ドキュメント名 | ファイル名 | 作成単位 | 説明 | 入力元 | 後続への影響 |
|---|---|---|---|---|---|---|
| IT-001_{FEAT-ID} | 結合テスト仕様・結果書 | `IT-001_{FEAT-ID}_{機能名}.md` | 機能別 | 機能単位の FE/BE 間・外部システム連携の結合テストケースと結果 | BSD-008, DSD-003, DSD-005, IMP-001, IMP-002 | ST-001 |
| IT-002 | API 結合テスト仕様・結果書 | `IT-002_api-integration-test.md` | システム共通 | 全 API エンドポイントの実動作確認・レスポンス検証（全機能横断） | DSD-003, IT-001 | ST-001 |
| IT-003 | 不具合管理票（結合テスト） | `IT-003_defect-list.md` | システム共通 | バグ ID・発生箇所（FEAT-ID 付）・重大度・対応状況・修正結果（全機能を集約） | IT-001, IT-002 | ST-001 |

### フェーズ 6: システムテスト (ST)

> **配置先**: すべて `projects/PRJ-{NNN}/ST/`。

| ドキュメントID | ドキュメント名 | ファイル名 | 説明 | 入力元 | 後続への影響 |
|---|---|---|---|---|---|
| ST-001 | システムテスト仕様・結果書 | `ST-001_system-test.md` | シナリオベースの E2E テストケース・業務フロー全体の検証結果 | BSD-008, REQ-003, REQ-005, IT-001 | UAT-001 |
| ST-002 | 性能テスト仕様・結果書 | `ST-002_performance-test.md` | 負荷試験・レスポンスタイム計測・スループット確認 | REQ-006, ST-001 | UAT-001 |
| ST-003 | セキュリティテスト仕様・結果書 | `ST-003_security-test.md` | 脆弱性診断・認証/認可テスト・通信暗号化確認 | REQ-006, BSD-002, ST-001 | UAT-001 |
| ST-004 | 不具合管理票（システムテスト） | `ST-004_defect-list.md` | バグ ID・発生箇所・重大度・対応状況・修正結果 | ST-001, ST-002, ST-003 | UAT-001 |

### フェーズ 7: 受入テスト (UAT)

> **配置先**: すべて `projects/PRJ-{NNN}/UAT/`。

| ドキュメントID | ドキュメント名 | ファイル名 | 説明 | 入力元 | 後続への影響 |
|---|---|---|---|---|---|
| UAT-001 | 受入テスト仕様・結果書 | `UAT-001_acceptance-test.md` | ユーザー視点のシナリオテスト・業務要件充足確認 | REQ-001, REQ-002, REQ-003, ST-001 | REL-001 |
| UAT-002 | 不具合管理票（受入テスト） | `UAT-002_defect-list.md` | バグ ID・発生箇所・重大度・対応状況・修正結果 | UAT-001 | REL-001 |
| UAT-003 | 受入完了報告書 | `UAT-003_acceptance-report.md` | テスト合否判定・残課題・リリース可否判断 | UAT-001, UAT-002 | REL-001 |

### フェーズ 8: リリース (REL)

> **配置先**: すべて `projects/PRJ-{NNN}/REL/`。

| ドキュメントID | ドキュメント名 | ファイル名 | 説明 | 入力元 | 後続への影響 |
|---|---|---|---|---|---|
| REL-001 | リリース計画書 | `REL-001_release-plan.md` | リリーススケジュール・リリース判定基準・関係者承認フロー | UAT-003 | REL-002 |
| REL-002 | リリース手順書 | `REL-002_release-procedure.md` | デプロイ手順・DB マイグレーション手順・ロールバック手順 | IMP-003, IMP-004, REL-001 | OPS-001 |
| REL-003 | リリース完了報告書 | `REL-003_release-report.md` | リリース実施結果・インシデント有無・本番確認チェックリスト | REL-002 | OPS-001 |

### フェーズ 9: 運用・保守 (OPS)

> **配置先**: すべて `docs/OPS/`（マスタドキュメント）。

| ドキュメントID | ドキュメント名 | ファイル名 | 説明 | 入力元 | 後続への影響 |
|---|---|---|---|---|---|
| OPS-001 | 運用マニュアル | `OPS-001_operations-manual.md` | 日常運用手順・監視項目・定期作業・問い合わせ対応フロー | REL-003, IMP-003 | - |
| OPS-002 | 障害対応手順書 | `OPS-002_incident-response.md` | 障害分類・エスカレーションフロー・復旧手順・ログ確認方法 | IMP-004, OPS-001 | - |
| OPS-003 | 変更管理手順書 | `OPS-003_change-management.md` | 機能追加・バグ修正時の変更申請・レビュー・適用手順 | REL-002 | - |
| OPS-004 | バックアップ・リストア手順書 | `OPS-004_backup-restore.md` | DB バックアップスケジュール・世代管理・リストア手順 | DSD-004, IMP-004 | - |

---

## TDD 統合方針

- DSD-008（単体テスト設計書）を起点に Red→Green→Refactor サイクルで実装する
- TDD の方法論（テストの書き方・テストダブル・FIRST 原則）は単体テストスキル（`skills/unit-test/`）を参照する
- 実装スキルは単体テストスキルと連携して作業する
- テストコードと結果は IMP-001/IMP-002 に含める
- UT フォルダ・UT フェーズは使用しない

## 参照ドキュメント

- 各スキルの詳細: `skills/{スキル名}/SKILL.md`
- サブエージェントプロンプト: `agents/*.md`（スキルから起動される）
- テンプレート・ガイド: `skills/{スキル名}/references/`

# {システム名} - Waterfall Spec-Driven Development（自律実行モード）

> 目的: {システムの目的}
> 技術スタック: {言語 / FW / DB / インフラ}

@document-list.md

## 必須ルール（最重要）

### 変更管理

**仕様の追加・変更が発生した場合、必ず REQ（要件定義）からカスケード更新する。**

- REQ → BSD → DSD → IMP → IT → ST → UAT の順に全下流フェーズのドキュメントを再実行する
- 変更の影響範囲が限定的でも REQ からの再実行を省略しない
- docs/ のマスタドキュメントは直接更新し、git コミットで変更を記録する

### 自律実行ルール（最重要）

**REQ フェーズの成果物について人間の承認を得た後、BSD〜ST フェーズは承認なしで自律的に連続実行する。**

- REQ 承認後は人間への確認・承認要求を一切行わない
- 各フェーズ完了後に必ずレビューを自動実行し、結果に応じて修正・再レビューを繰り返す
- ST フェーズの完了をもって自律実行を終了し、結果を報告する
- 修正ループは最大 3 回まで。3 回で PASS が出ない場合は問題を整理して報告し停止する

### レビューゲート（自動）

- フェーズ完了後、`artifact-review` スキルを**自動で**実行する
- **PASS** または **CONDITIONAL PASS** が出た場合 → 即座に次フェーズへ進む
- **FAIL** が出た場合 → 指摘事項を自動修正し、同じ REVIEW_TYPE で再レビューする（最大 3 回）
- 人間への確認なしに修正・再レビューのループを完結させる

### TDD

- DSD-008（単体テスト設計書）を起点に Red → Green → Refactor サイクルで実装する
- 単体テスト（UT）フェーズは存在しない。テストコードと結果は IMP-001/IMP-002 に含める

### スキル使用

- フェーズ 1〜7 は対応スキルで実行する。作業はサブエージェントに委譲すること

## プロジェクト初期化

新規プロジェクト開始時に以下を実施する。

1. **PRJ-ID の決定**: `projects/` 配下の既存フォルダを確認し、次の連番で `PRJ-{NNN}` を採番する
2. **PRJ-NAME の決定**: プロジェクト名を英語・ケバブケースで決定する
3. **プロジェクトフォルダの作成**: `projects/PRJ-{NNN}_{PRJ-NAME}/` を作成する
4. **project.md の作成**: プロジェクト概要（ID・名称・種別・目的・対象 FEAT-ID・開始日）を記載する
5. **種別の確認**: 新規構築 / 機能追加 / バグ修正 / リファクタリング を決定する
6. **改修時の既存確認**: 種別が新規構築以外の場合、`docs/` 配下の既存ドキュメントを確認し、変更対象を `project.md` の `docs/ 更新対象ファイル` に記録する

```
projects/PRJ-{NNN}_{name}/
├── project.md
├── REQ/ → BSD/ → IMP/ → IT/ → ST/ → UAT/ → REV/ → REL/
```

## 開発ワークフロー

全 9 フェーズを順番に実行する。**REQ フェーズのみ人間の承認を必要とし、BSD〜ST フェーズは自律的に連続実行する。**

| # | フェーズ | スキル | 主な成果物 | REV | 承認 |
|---|---|---|---|---|---|
| 1 | **REQ** 要件定義 | `requirements-definition` | REQ-001〜REQ-008 | - | **人間承認必須** |
| 2 | **BSD** 基本設計 | `basic-design-doc` | BSD-001〜BSD-010 | REV-001（自動） | 自律実行 |
| 3 | **DSD** 詳細設計 | `detailed-design-doc` | DSD-001〜DSD-009（FEAT 単位） | REV-002（自動） | 自律実行 |
| 4 | **IMP** 実装（TDD） | `implementation` | IMP-001〜IMP-005 + ソースコード | REV-003（自動） | 自律実行 |
| 5 | **IT** 結合テスト | `integration-test` | IT-001〜IT-003（FEAT 単位） | - | 自律実行 |
| 6 | **ST** システムテスト | `system-test` | ST-001〜ST-004 | REV-004（自動） | 自律実行 |
| 7 | **UAT** 受入テスト | `acceptance-test` | UAT-001〜UAT-003 | - | 人間承認後 |
| 8 | **REL** リリース | （手動） | REL-001〜REL-003 | - | 人間承認後 |
| 9 | **OPS** 運用・保守 | （手動） | OPS-001〜OPS-004 | - | 人間承認後 |

> - DSD / IMP / IT は FEAT 単位で並行実行可能
> - IT は全 FEAT の IMP 完了後に実行。ST は全 FEAT の IT 完了後に実行
> - BSD-009（DDD 戦略設計）・BSD-010（データアーキテクチャ）は BSD フェーズで作成
> - DSD-009（DDD 戦術設計）は DSD フェーズで各 FEAT の最初に作成

## スキル起動手順

全スキル共通の 4 ステップで起動する。

1. `skills/{スキル名}/SKILL.md` を Read で読み込む
2. SKILL.md の「エージェント起動」に従い `agents/{スキル名}.md` を Read で読み込む
3. パラメータを実際の値に置換してプロンプトを作成する
4. Task サブエージェント（general-purpose）を起動し、置換済みプロンプトを渡す

### パラメータ一覧

| フェーズ | スキル名 | パラメータ |
|---|---|---|
| REQ | `requirements-definition` | `{{PROJECT_ID}}`, `{{PROJECT_NAME}}` |
| BSD | `basic-design-doc` | `{{PROJECT_ID}}`, `{{PROJECT_NAME}}` |
| DSD | `detailed-design-doc` | `{{PROJECT_ID}}`, `{{PROJECT_NAME}}`, `{{FEAT_ID}}`, `{{FEAT_NAME}}` |
| IMP | `implementation` | `{{PROJECT_ID}}`, `{{PROJECT_NAME}}`, `{{FEAT_ID}}`, `{{FEAT_NAME}}` |
| IT | `integration-test` | `{{PROJECT_ID}}`, `{{PROJECT_NAME}}`, `{{FEAT_ID}}`, `{{FEAT_NAME}}` |
| ST | `system-test` | `{{PROJECT_ID}}`, `{{PROJECT_NAME}}` |
| UAT | `acceptance-test` | `{{PROJECT_ID}}`, `{{PROJECT_NAME}}` |
| REV | `artifact-review` | `{{PROJECT_ID}}`, `{{PROJECT_NAME}}`, `{{REVIEW_TYPE}}` |

### artifact-review の REVIEW_TYPE

| REVIEW_TYPE | 実行タイミング | 出力 |
|---|---|---|
| `REQ-BSD` | BSD 完了時 | REV-001_req-to-bsd.md |
| `BSD-DSD` | DSD 完了時 | REV-002_bsd-to-dsd.md |
| `DSD-IMP` | IMP 完了時 | REV-003_dsd-to-imp.md |
| `IMP-TEST` | IT/ST 完了時 | REV-004_imp-to-test.md |

## フェーズ別実行手順

### Phase 1: REQ（要件定義）— 人間承認フェーズ

1. `requirements-definition` スキルを起動する
2. 成果物: REQ-001〜REQ-004 → `projects/`、REQ-005〜REQ-008 → `docs/REQ/`
3. **[人間承認必須]** 成果物の内容を提示し、ユーザーの承認を得る
4. 承認が得られたら自律実行モードを開始し、ST 完了まで人間への確認を行わない

---

> **以下 Phase 2〜6 は自律実行。承認なしで連続実行する。**
>
> 各フェーズの自動レビューで FAIL が出た場合の修正ループ手順:
> 1. レビュー結果（REV ドキュメント）の指摘事項を読み込む
> 2. 指摘された成果物を修正する
> 3. 同一 REVIEW_TYPE で `artifact-review` を再実行する
> 4. PASS / CONDITIONAL PASS が出るまで繰り返す（最大 3 回）
> 5. 3 回繰り返しても PASS が出ない場合は修正不能な問題を整理して報告し、自律実行を停止する

---

### Phase 2: BSD（基本設計）— 自律実行

1. `basic-design-doc` スキルを起動する
2. 成果物: BSD-001〜BSD-007, BSD-009, BSD-010 → `docs/BSD/`、BSD-008 → `projects/`
3. **[自動レビュー]** `artifact-review`（REVIEW_TYPE: `REQ-BSD`）を実行する
   - **PASS / CONDITIONAL PASS** → Phase 3 へ即座に進む
   - **FAIL** → 指摘事項を修正し再レビュー（最大 3 回ループ）

### Phase 3: DSD（詳細設計）— 自律実行・FEAT 単位で並行可

1. 各 FEAT に対して `detailed-design-doc` スキルを起動する
2. DSD-009（DDD 戦術設計）→ DSD-001〜DSD-008 の順に生成する
3. 成果物: すべて `docs/DSD/FEAT-{NNN}_{name}/`（DSD-007 のみ `docs/DSD/_common/`）
4. **[自動レビュー]** 全 FEAT 完了後に `artifact-review`（REVIEW_TYPE: `BSD-DSD`）を実行する
   - **PASS / CONDITIONAL PASS** → Phase 4 へ即座に進む
   - **FAIL** → 指摘事項を修正し再レビュー（最大 3 回ループ）

### Phase 4: IMP（実装・TDD）— 自律実行・FEAT 単位で並行可

1. 各 FEAT に対して `implementation` スキルを起動する
2. DSD-008 を起点に Red → Green → Refactor で実装する
3. 成果物: IMP-001〜IMP-002 → `projects/IMP/FEAT-{NNN}/`、IMP-003〜IMP-005 → `projects/IMP/`
4. **[自動レビュー]** 全 FEAT 完了後に `artifact-review`（REVIEW_TYPE: `DSD-IMP`）を実行する
   - **PASS / CONDITIONAL PASS** → Phase 5 へ即座に進む
   - **FAIL** → 指摘事項を修正し再レビュー（最大 3 回ループ）

### Phase 5: IT（結合テスト）— 自律実行・FEAT 単位で並行可

1. 各 FEAT に対して `integration-test` スキルを起動する（全 FEAT の IMP 完了が前提）
2. 成果物: IT-001 → `projects/IT/FEAT-{NNN}/`、IT-002〜IT-003 → `projects/IT/`
3. 全 FEAT の IT 完了後、Phase 6 へ即座に進む

### Phase 6: ST（システムテスト）— 自律実行

1. 全 FEAT の IT 完了後に `system-test` スキルを起動する
2. 成果物: ST-001〜ST-004 → `projects/ST/`
3. **[自動レビュー]** `artifact-review`（REVIEW_TYPE: `IMP-TEST`）を実行する
   - **PASS / CONDITIONAL PASS** → **自律実行完了**。結果サマリーを人間に報告する
   - **FAIL** → 指摘事項を修正し再レビュー（最大 3 回ループ）

---

> **ST フェーズ完了をもって自律実行モードを終了する。**
> 完了報告には以下を含める:
> - 各フェーズの実行結果サマリー
> - レビューで検出・修正した問題の一覧
> - 生成されたすべての成果物パス一覧
> - 次フェーズ（UAT）に向けた引き継ぎ事項

---

### Phase 7: UAT（受入テスト）

1. ST 完了報告を確認後、ユーザーの指示を待つ
2. `acceptance-test` スキルを起動する
3. 成果物: UAT-001〜UAT-003 → `projects/UAT/`

### Phase 8-9: REL / OPS

- REL-001〜REL-003 → `projects/REL/`（手動作成）
- OPS-001〜OPS-004 → `docs/OPS/`（手動更新）

## 参照先

- 全成果物の仕様・命名規則・配置先: @document-list.md
- スキル定義: `skills/{スキル名}/SKILL.md`
- サブエージェントプロンプト: `agents/*.md`

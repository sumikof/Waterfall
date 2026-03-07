# {システム名} - Waterfall Spec-Driven Development (Auto Mode)

> 目的: {システムの目的}
> 技術スタック: {言語 / FW / DB / インフラ}

@document-list.md

## 必須ルール（最重要）

### 変更管理

**仕様の追加・変更が発生した場合、必ず REQ（要件定義）からカスケード更新する。**

- REQ → BSD → DSD → IMP → IT → ST の順に全下流フェーズのドキュメントを再実行する
- 変更の影響範囲が限定的でも REQ からの再実行を省略しない
- docs/ のマスタドキュメントは直接更新し、git コミットで変更を記録する

### 自動実行モード

**REQ（要件定義書）完成後は、ユーザー確認・レビューゲートを一切挟まず BSD → DSD → IMP → IT → ST まで自律的に完了させる。**

- レビュー（artifact-review）はスキップする
- フェーズ間で停止・確認・承認を求めない
- エラーが発生した場合のみ処理を中断し、ユーザーに報告する

### TDD

- DSD-008（単体テスト設計書）を起点に Red → Green → Refactor サイクルで実装する
- 単体テスト（UT）フェーズは存在しない。テストコードと結果は IMP-001/IMP-002 に含める

### スキル使用

- フェーズ 1〜6 は対応スキルで実行する。作業はサブエージェントに委譲すること

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
├── REQ/ → BSD/ → IMP/ → IT/ → ST/
```

## 開発ワークフロー

REQ フェーズ完了後は Phase 2〜6 を自動で連続実行する。ユーザーの介入は不要。

| # | フェーズ | スキル | 主な成果物 | 確認 |
|---|---|---|---|---|
| 1 | **REQ** 要件定義 | `requirements-definition` | REQ-001〜REQ-008 | ユーザー確認あり（唯一） |
| 2 | **BSD** 基本設計 | `basic-design-doc` | BSD-001〜BSD-010 | 自動 |
| 3 | **DSD** 詳細設計 | `detailed-design-doc` | DSD-001〜DSD-009（FEAT 単位） | 自動 |
| 4 | **IMP** 実装（TDD） | `implementation` | IMP-001〜IMP-005 + ソースコード | 自動 |
| 5 | **IT** 結合テスト | `integration-test` | IT-001〜IT-003（FEAT 単位） | 自動 |
| 6 | **ST** システムテスト | `system-test` | ST-001〜ST-004 | 自動（ここで完了） |

> - DSD / IMP / IT は FEAT 単位で並行実行可能
> - IT は全 FEAT の IMP 完了後に実行。ST は全 FEAT の IT 完了後に実行
> - BSD-009（DDD 戦略設計）・BSD-010（データアーキテクチャ）は BSD フェーズで作成
> - DSD-009（DDD 戦術設計）は DSD フェーズで各 FEAT の最初に作成
> - UAT / REL / OPS は本モードのスコープ外（必要に応じて手動実施）

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

## フェーズ別実行手順

### Phase 1: REQ（要件定義）← ユーザー確認あり

1. `requirements-definition` スキルを起動する
2. 成果物: REQ-001〜REQ-004 → `projects/`、REQ-005〜REQ-008 → `docs/REQ/`
3. **[唯一の確認ポイント]** 要件定義書の内容をユーザーに確認し、承認を得る
4. 承認後は Phase 2 以降を自動で連続実行する（以降は一切確認しない）

### Phase 2: BSD（基本設計）← 自動

1. `basic-design-doc` スキルを起動する
2. 成果物: BSD-001〜BSD-007, BSD-009, BSD-010 → `docs/BSD/`、BSD-008 → `projects/`
3. 完了後、即座に Phase 3 へ進む

### Phase 3: DSD（詳細設計）← 自動・FEAT 単位で並行可

1. 各 FEAT に対して `detailed-design-doc` スキルを起動する
2. DSD-009（DDD 戦術設計）→ DSD-001〜DSD-008 の順に生成する
3. 成果物: すべて `docs/DSD/FEAT-{NNN}_{name}/`（DSD-007 のみ `docs/DSD/_common/`）
4. 全 FEAT 完了後、即座に Phase 4 へ進む

### Phase 4: IMP（実装・TDD）← 自動・FEAT 単位で並行可

1. 各 FEAT に対して `implementation` スキルを起動する
2. DSD-008 を起点に Red → Green → Refactor で実装する
3. 成果物: IMP-001〜IMP-002 → `projects/IMP/FEAT-{NNN}/`、IMP-003〜IMP-005 → `projects/IMP/`
4. 全 FEAT 完了後、即座に Phase 5 へ進む

### Phase 5: IT（結合テスト）← 自動・FEAT 単位で並行可

1. 各 FEAT に対して `integration-test` スキルを起動する（全 FEAT の IMP 完了が前提）
2. 成果物: IT-001 → `projects/IT/FEAT-{NNN}/`、IT-002〜IT-003 → `projects/IT/`
3. 全 FEAT 完了後、即座に Phase 6 へ進む

### Phase 6: ST（システムテスト）← 自動・完了報告

1. 全 FEAT の IT 完了後に `system-test` スキルを起動する
2. 成果物: ST-001〜ST-004 → `projects/ST/`
3. ST 完了をもって自動実行フェーズ終了。ユーザーに完了を報告する

### Phase 7 以降: UAT / REL / OPS

- 本モードのスコープ外。必要に応じてユーザーが手動で実施する

## 参照先

- 全成果物の仕様・命名規則・配置先: @document-list.md
- スキル定義: `skills/{スキル名}/SKILL.md`
- サブエージェントプロンプト: `agents/*.md`

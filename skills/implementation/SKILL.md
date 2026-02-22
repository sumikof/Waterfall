---
name: implementation
description: |
  実装スキル。詳細設計書（DSD）を入力とし、テスト駆動開発（TDD）の Red→Green→Refactor サイクルで実装を進め、実装完了報告書（IMP）を出力する。

  以下の場合に使用する：
  - 「実装して」「コードを書いて」「TDDで開発して」などの依頼
  - 詳細設計書（DSD-001〜DSD-008）が揃っている機能の実装
  - バックエンド・フロントエンド・DB マイグレーションの実装
  - 実装完了後に IMP-001〜IMP-004 の報告書を作成する場合

  入力: docs/DSD/ 以下の DSD-001〜DSD-008
  出力: projects/PRJ-{NNN}/IMP/ 以下の IMP-001〜IMP-004
  配置ルール・ドキュメント定義 → references/document-list.md
---

# 実装スキル（TDD）

## 概要

詳細設計書を読み込み、TDD サイクルで機能ごとに実装し、実装完了報告書を作成する。

参照ファイル：
- TDD サイクル詳細 → `references/tdd-workflow.md`
- IMP ドキュメントテンプレート → `references/imp-templates.md`
- 配置ルール・ドキュメント定義 → `references/document-list.md`

## 並行作業スコープ

**このスキルは 1回の起動で 1つの FEAT-ID を担当する。**
複数の FEAT-ID は別セッションで並行して進める。

```
並行作業の例（FEAT-001 と FEAT-002 を同時に進める）:
  セッション A: FEAT-001 担当 → IMP-001_FEAT-001, IMP-002_FEAT-001 を作成
  セッション B: FEAT-002 担当 → IMP-001_FEAT-002, IMP-002_FEAT-002 を作成
  最後にまとめ: IMP-004 に両機能のマイグレーションを統合
```

**IMP-004（DB マイグレーション）は競合注意**:
- 各セッションは自分の FEAT-ID 分のマイグレーションを `IMP-004_FEAT-{NNN}_draft.md` として作成する
- 全 FEAT-ID の実装完了後、プロジェクト担当者が実行順序を調整して `IMP-004_db-migration.md` に統合する

## 前提確認

実装開始前に以下を確認する：

1. **対象 FEAT-ID の確認**: どの機能（FEAT-{NNN}）を実装するか
2. **プロジェクト ID の確認**: PRJ-{NNN} と出力先ディレクトリ
3. **実装範囲の確認**: バックエンドのみ / フロントエンドのみ / 両方
4. **FEAT 間依存の確認**: 依存する他の FEAT-ID が未実装でないか（DSD-001/002 の依存関係セクションを確認）
5. **初回構築かどうか**: IMP-003（環境構築手順書）が必要か

## 入力ドキュメントの読み込み順序

```
必須（最初に読む）
  docs/DSD/_common/DSD-007_coding-guidelines.md              ← コーディング規約（システム共通）
  docs/DSD/FEAT-{NNN}_{機能名}/DSD-008_{FEAT-ID}_{機能名}.md ← 単体テスト設計書

バックエンド実装時
  docs/DSD/FEAT-{NNN}_{機能名}/DSD-001_{FEAT-ID}_{機能名}.md ← BE機能詳細設計
  docs/DSD/FEAT-{NNN}_{機能名}/DSD-003_{FEAT-ID}_{機能名}.md ← API詳細設計
  docs/DSD/FEAT-{NNN}_{機能名}/DSD-004_{FEAT-ID}_{機能名}.md ← DB詳細設計
  docs/DSD/FEAT-{NNN}_{機能名}/DSD-005_{FEAT-ID}_{機能名}.md ← 外部IF（存在する場合のみ）
  docs/DSD/FEAT-{NNN}_{機能名}/DSD-006_{FEAT-ID}_{機能名}.md ← バッチ（存在する場合のみ）

フロントエンド実装時
  docs/DSD/FEAT-{NNN}_{機能名}/DSD-002_{FEAT-ID}_{機能名}.md ← FE詳細設計
  docs/DSD/FEAT-{NNN}_{機能名}/DSD-003_{FEAT-ID}_{機能名}.md ← API詳細設計
```

## TDD ワークフロー（機能ごとに繰り返す）

詳細な TDD 手順 → `references/tdd-workflow.md`

### ステップ 1: テストリスト作成（DSD-008 から）

DSD-008 の各テストケースをリスト化する。実装順序は依存関係の低いものから。

### ステップ 2: Red（失敗するテストを書く）

1. DSD-008 の1テストケースを選ぶ
2. テストコードを書く（実装コードなしで実行し、意図した理由で失敗することを確認）
3. テスト名は DSD-008 のテストケースIDと対応させる

### ステップ 3: Green（最小実装でテストを通す）

1. DSD-001/002/003/004 の仕様に従い、そのテストだけを通す最小限の実装を書く
2. 設計との差異が生じた場合は記録しておく（IMP-001/002 の差異一覧に記載）
3. テストが GREEN になったことを確認する

### ステップ 4: Refactor（DSD-007 に従いリファクタリング）

1. DSD-007（コーディング規約）に従い命名・構造を整える
2. テストが引き続き GREEN であることを確認する
3. 重複排除・可読性向上のみ行う（振る舞いを変えない）

### ステップ 5: 次のテストケースへ（ステップ 2〜4 を繰り返す）

DSD-008 の全テストケースが GREEN になるまで繰り返す。

## DB マイグレーション

DSD-004 を元に、マイグレーションスクリプトを作成する。

**並行作業時の手順**:
1. 各 FEAT セッションは自分の担当分を `IMP-004_FEAT-{NNN}_draft.md` として作成する（`references/imp-templates.md` の IMP-004 テンプレートを使用）
2. 全 FEAT-ID の実装完了後、実行順序（テーブル依存関係）を調整して `IMP-004_db-migration.md` に統合する
3. 統合時に FEAT 間のテーブル依存（外部キー参照など）を考慮した実行順序を確定する

## 出力ドキュメントの作成

`references/imp-templates.md` のテンプレートを使い、機能ごとに以下を作成する：

| ドキュメント | 作成タイミング | 配置先 | 並行作業 |
|---|---|---|---|
| IMP-001_{FEAT-ID}_{機能名}.md | BE 実装完了後 | `projects/PRJ-{NNN}/IMP/FEAT-{NNN}_{機能名}/` | 各 FEAT 独立 |
| IMP-002_{FEAT-ID}_{機能名}.md | FE 実装完了後 | `projects/PRJ-{NNN}/IMP/FEAT-{NNN}_{機能名}/` | 各 FEAT 独立 |
| IMP-003_environment-setup.md | 初回構築時のみ（1セッションで作成） | `projects/PRJ-{NNN}/IMP/` | 単独作成 |
| IMP-004_FEAT-{NNN}_draft.md | DB 変更がある FEAT の実装完了後 | `projects/PRJ-{NNN}/IMP/` | 各 FEAT 独立で下書き作成 |
| IMP-004_db-migration.md | 全 FEAT 完了後に統合 | `projects/PRJ-{NNN}/IMP/` | draft をマージして完成 |

## 設計差異の記録原則

実装中に DSD との差異が生じた場合：
1. 差異内容・理由を記録する（IMP-001/002 の「設計差異一覧」に記載）
2. 差異が設計変更を要する場合はユーザーに確認し、必要なら DSD を更新する
3. 既知の制限事項（将来対応）は「既知の制限事項」セクションに記載する

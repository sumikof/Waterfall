# DSD-004 データベース詳細設計書 テンプレート

## 目次
1. 対象テーブル一覧
2. テーブル詳細定義
3. インデックス設計
4. マイグレーション方針
5. 後続フェーズへの影響

---

## セクション構成

```markdown
## 1. 対象テーブル一覧

（BSD-006 のテーブル一覧からこの機能（FEAT-ID）に関わるテーブルを抽出）

| テーブル名 | 論理名 | 操作種別 | 説明 |
|---|---|---|---|
| `{table_name}` | {論理名} | 新規作成 / 既存変更 / 参照のみ | {概要} |

---

## 2. テーブル詳細定義

### 2.1 `{table_name}`（{論理名}）

**概要:** {テーブルの役割}
**操作種別:** 新規作成 / 既存変更（変更内容: {変更点}） / 参照のみ

#### カラム定義

| カラム名 | 論理名 | データ型 | 長さ | NULL | デフォルト | 備考 |
|---|---|---|---|---|---|---|
| `id` | ID | UUID | - | NOT NULL | `gen_random_uuid()` | PK |
| `user_id` | ユーザーID | UUID | - | NOT NULL | - | FK: `users.id` |
| `name` | 名称 | VARCHAR | 255 | NOT NULL | - | |
| `description` | 説明 | TEXT | - | NULL | - | |
| `amount` | 金額 | INTEGER | - | NOT NULL | - | 1以上 |
| `status` | ステータス | VARCHAR | 20 | NOT NULL | `'active'` | 許可値: `active`, `inactive`, `deleted` |
| `metadata` | メタデータ | JSONB | - | NULL | - | 拡張用 |
| `is_deleted` | 論理削除フラグ | BOOLEAN | - | NOT NULL | `false` | - |
| `created_at` | 作成日時 | TIMESTAMPTZ | - | NOT NULL | `NOW()` | |
| `updated_at` | 更新日時 | TIMESTAMPTZ | - | NOT NULL | `NOW()` | トリガーで自動更新 |
| `deleted_at` | 削除日時 | TIMESTAMPTZ | - | NULL | - | 論理削除時に設定 |
| `created_by` | 作成者ID | UUID | - | NULL | - | FK: `users.id` |
| `updated_by` | 更新者ID | UUID | - | NULL | - | FK: `users.id` |

#### 制約

| 制約名 | 種別 | カラム | 参照先 | 説明 |
|---|---|---|---|---|
| `{table}_pkey` | PRIMARY KEY | `id` | - | 主キー |
| `{table}_user_id_fkey` | FOREIGN KEY | `user_id` | `users(id)` | ユーザー参照 |
| `{table}_name_unique` | UNIQUE | `name`, `user_id` | - | ユーザー内で名称重複不可 |
| `{table}_amount_check` | CHECK | `amount` | - | `amount >= 1` |

（テーブル数分繰り返す）

---

## 3. インデックス設計

| インデックス名 | テーブル | カラム | 種別 | 用途・クエリ例 |
|---|---|---|---|---|
| `idx_{table}_user_id` | `{table_name}` | `user_id` | B-tree | ユーザー別一覧取得 |
| `idx_{table}_status` | `{table_name}` | `status` | B-tree | ステータスフィルタ |
| `idx_{table}_created_at` | `{table_name}` | `created_at DESC` | B-tree | 新着順ソート |
| `idx_{table}_user_created` | `{table_name}` | `user_id, created_at DESC` | 複合B-tree | ユーザー別新着一覧 |
| `idx_{table}_name_search` | `{table_name}` | `name` | GIN (pg_trgm) | 部分一致検索 |
| `idx_{table}_deleted_at` | `{table_name}` | `deleted_at` | Partial (`WHERE deleted_at IS NULL`) | 論理削除除外 |

**パーティショニング（必要な場合）:**
- パーティションキー: {カラム名}
- 方式: RANGE / LIST / HASH

---

## 4. マイグレーション方針

### 4.1 マイグレーションファイル

| ファイル名 | 内容 | 実行順 |
|---|---|---|
| `V{version}__create_{table_name}.sql` | テーブル新規作成 | 1 |
| `V{version}__add_index_{table_name}.sql` | インデックス追加 | 2 |
| `V{version}__seed_{table_name}.sql` | 初期データ投入（マスタのみ） | 3 |

### 4.2 マイグレーションSQL概要

**テーブル作成:**
```sql
CREATE TABLE {table_name} (
  id          UUID        NOT NULL DEFAULT gen_random_uuid(),
  user_id     UUID        NOT NULL,
  name        VARCHAR(255) NOT NULL,
  description TEXT,
  amount      INTEGER     NOT NULL CHECK (amount >= 1),
  status      VARCHAR(20) NOT NULL DEFAULT 'active',
  is_deleted  BOOLEAN     NOT NULL DEFAULT false,
  created_at  TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  updated_at  TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  deleted_at  TIMESTAMPTZ,
  created_by  UUID,
  updated_by  UUID,
  CONSTRAINT {table}_pkey PRIMARY KEY (id),
  CONSTRAINT {table}_user_id_fkey FOREIGN KEY (user_id) REFERENCES users(id)
);
```

**updated_at 自動更新トリガー:**
```sql
CREATE OR REPLACE FUNCTION update_updated_at_column()
RETURNS TRIGGER AS $$
BEGIN
  NEW.updated_at = NOW();
  RETURN NEW;
END;
$$ language 'plpgsql';

CREATE TRIGGER update_{table}_updated_at
  BEFORE UPDATE ON {table_name}
  FOR EACH ROW EXECUTE FUNCTION update_updated_at_column();
```

### 4.3 ロールバック手順

| 操作 | ロールバック SQL |
|---|---|
| テーブル作成 | `DROP TABLE IF EXISTS {table_name};` |
| カラム追加 | `ALTER TABLE {table_name} DROP COLUMN IF EXISTS {column};` |
| インデックス追加 | `DROP INDEX IF EXISTS {index_name};` |

---

## 5. 後続フェーズへの影響

| 影響先 | 内容 |
|---|---|
| IMP-001_{FEAT-ID} | ORMエンティティ定義・マイグレーション実装 |
| IMP-004 | 全機能のマイグレーションスクリプト統合 |
| OPS-004 | バックアップ対象テーブルの追加 |
```

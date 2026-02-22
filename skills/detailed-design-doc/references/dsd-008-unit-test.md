# DSD-008 単体テスト設計書 テンプレート

## 目次
1. テスト対象・方針
2. バックエンド単体テスト設計
3. フロントエンド単体テスト設計
4. テストデータ設計
5. 後続フェーズへの影響

---

## セクション構成

```markdown
## 1. テスト対象・方針

### 1.1 テスト対象

（DSD-001・DSD-002・DSD-003 から対象クラス/コンポーネント/エンドポイントを列挙）

| No. | テスト対象 | ファイルパス | テスト種別 |
|---|---|---|---|
| 1 | `{FeatureService}` | `src/modules/{feature}/{feature}.service.ts` | バックエンドUT |
| 2 | `{FeatureController}` | `src/modules/{feature}/{feature}.controller.ts` | バックエンドUT |
| 3 | `{FeatureForm}` | `src/components/{feature}/Form.tsx` | フロントエンドUT |

### 1.2 テスト方針

- **バックエンド**: サービス層のビジネスロジックを重点的にテストする。DBアクセスはリポジトリをモック化する
- **フロントエンド**: コンポーネントのレンダリング・ユーザー操作・状態変化をテストする。APIはモック化する
- **モック対象**: 外部DB・外部API・メール送信・時刻（`Date.now()`）など非決定的な要素はすべてモックする
- **カバレッジ目標**: ライン 80% 以上、ブランチ 80% 以上

---

## 2. バックエンド単体テスト設計

### 2.1 {ClassName}（{ファイルパス}）

**テストフレームワーク:** Jest / pytest / etc.

#### テストケース一覧

| TC-ID | メソッド名 | テストシナリオ | 分類 | 期待結果 |
|---|---|---|---|---|
| TC-BE-001 | `findById` | 存在するIDを渡した場合 | 正常系 | 対応するエンティティを返す |
| TC-BE-002 | `findById` | 存在しないIDを渡した場合 | 異常系 | `NotFoundException` をスロー |
| TC-BE-003 | `create` | 正常なデータを渡した場合 | 正常系 | 作成されたエンティティを返す |
| TC-BE-004 | `create` | 名称が重複する場合 | 異常系 | `ConflictException` をスロー |
| TC-BE-005 | `create` | 名称が空文字の場合 | 異常系 | `ValidationException` をスロー |
| TC-BE-006 | `update` | 存在するIDと正常データ | 正常系 | 更新されたエンティティを返す |
| TC-BE-007 | `update` | 存在しないIDを渡した場合 | 異常系 | `NotFoundException` をスロー |
| TC-BE-008 | `delete` | 存在するIDを渡した場合 | 正常系 | 論理削除され void を返す |
| TC-BE-009 | `delete` | 存在しないIDを渡した場合 | 異常系 | `NotFoundException` をスロー |
| TC-BE-010 | `findAll` | 検索条件なし | 正常系 | ページネーション付き一覧を返す |
| TC-BE-011 | `findAll` | searchキーワードあり | 正常系 | キーワードに一致する件数を返す |
| TC-BE-012 | `findAll` | 0件の場合 | 境界値 | 空配列とtotal=0を返す |

#### テストコード概要（代表例）

```typescript
describe('FeatureService', () => {
  let service: FeatureService;
  let mockRepository: jest.Mocked<FeatureRepository>;

  beforeEach(async () => {
    const module = await Test.createTestingModule({
      providers: [
        FeatureService,
        {
          provide: FeatureRepository,
          useValue: {
            findById: jest.fn(),
            findAll: jest.fn(),
            save: jest.fn(),
            delete: jest.fn(),
          },
        },
      ],
    }).compile();

    service = module.get(FeatureService);
    mockRepository = module.get(FeatureRepository);
  });

  describe('findById', () => {
    it('TC-BE-001: 存在するIDを渡した場合、エンティティを返す', async () => {
      const mockEntity = { id: 'uuid', name: 'test' };
      mockRepository.findById.mockResolvedValue(mockEntity);

      const result = await service.findById('uuid');

      expect(result).toEqual(mockEntity);
      expect(mockRepository.findById).toHaveBeenCalledWith('uuid');
    });

    it('TC-BE-002: 存在しないIDを渡した場合、NotFoundException をスロー', async () => {
      mockRepository.findById.mockResolvedValue(null);

      await expect(service.findById('not-exist')).rejects.toThrow(NotFoundException);
    });
  });
});
```

（クラス数分繰り返す）

---

## 3. フロントエンド単体テスト設計

### 3.1 {ComponentName}（{ファイルパス}）

**テストフレームワーク:** Jest + React Testing Library

#### テストケース一覧

| TC-ID | テストシナリオ | 分類 | 期待結果 |
|---|---|---|---|
| TC-FE-001 | 初期レンダリング | 正常系 | フォームが正しく表示される |
| TC-FE-002 | 必須フィールドが空でサブミット | 異常系 | バリデーションエラーが表示される |
| TC-FE-003 | 正常なデータでサブミット | 正常系 | `onSubmit` コールバックが呼ばれる |
| TC-FE-004 | APIエラー時の表示 | 異常系 | エラーメッセージが表示される |
| TC-FE-005 | ローディング中の表示 | 正常系 | ボタンが無効化・スピナーが表示される |
| TC-FE-006 | 入力値のバリデーション（メール形式） | 異常系 | 不正メールでエラー表示 |
| TC-FE-007 | データ一覧が0件の場合 | 境界値 | 「データがありません」が表示される |
| TC-FE-008 | キーボード操作（Tab順序） | アクセシビリティ | フォーカスが正しい順序で移動する |

#### テストコード概要（代表例）

```tsx
describe('FeatureForm', () => {
  const mockOnSubmit = jest.fn();

  beforeEach(() => {
    mockOnSubmit.mockClear();
  });

  it('TC-FE-001: 初期レンダリング - フォームが正しく表示される', () => {
    render(<FeatureForm onSubmit={mockOnSubmit} />);

    expect(screen.getByLabelText('名称')).toBeInTheDocument();
    expect(screen.getByRole('button', { name: '送信' })).toBeEnabled();
  });

  it('TC-FE-002: 必須フィールドが空でサブミット - バリデーションエラー表示', async () => {
    render(<FeatureForm onSubmit={mockOnSubmit} />);

    await userEvent.click(screen.getByRole('button', { name: '送信' }));

    expect(screen.getByText('名称を入力してください')).toBeInTheDocument();
    expect(mockOnSubmit).not.toHaveBeenCalled();
  });

  it('TC-FE-003: 正常なデータでサブミット - onSubmit が呼ばれる', async () => {
    render(<FeatureForm onSubmit={mockOnSubmit} />);

    await userEvent.type(screen.getByLabelText('名称'), 'テスト名称');
    await userEvent.click(screen.getByRole('button', { name: '送信' }));

    expect(mockOnSubmit).toHaveBeenCalledWith({ name: 'テスト名称' });
  });
});
```

（コンポーネント数分繰り返す）

---

## 4. テストデータ設計

### 4.1 フィクスチャ（共通テストデータ）

```typescript
// fixtures/{feature}.fixture.ts

export const validFeatureData = {
  name: 'テスト名称',
  description: 'テスト説明',
  amount: 1000,
  categoryId: '550e8400-e29b-41d4-a716-446655440000',
};

export const invalidFeatureData = {
  emptyName: { ...validFeatureData, name: '' },
  invalidEmail: { ...validFeatureData, email: 'not-an-email' },
  negativeAmount: { ...validFeatureData, amount: -1 },
  tooLongName: { ...validFeatureData, name: 'a'.repeat(101) },
};

export const featureEntity = {
  id: '550e8400-e29b-41d4-a716-446655440001',
  ...validFeatureData,
  createdAt: new Date('2024-01-01T00:00:00Z'),
  updatedAt: new Date('2024-01-01T00:00:00Z'),
};
```

### 4.2 境界値テストデータ

| フィールド | 最小値テスト | 最大値テスト | 無効値テスト |
|---|---|---|---|
| `name` | 1文字 | 100文字 | 0文字（空）、101文字 |
| `amount` | 1 | 999999 | 0、-1、小数 |
| `tags` | 0件 | 10件 | 11件 |

---

## 5. 後続フェーズへの影響

| 影響先 | 内容 |
|---|---|
| UT-001_{FEAT-ID} | バックエンド単体テストの実施・結果記録 |
| UT-002_{FEAT-ID} | フロントエンド単体テストの実施・結果記録 |
```

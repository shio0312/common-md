# 過去の失敗パターン詳細

## Mermaid 関連

### quadrantChart 座標エラー（Mermaid v11）
**症状**: グラフが描画されない・座標が認識されない  
**原因**: Mermaid v11 から座標形式が変更された
```
# NG（旧形式）
quadrantChart
  点A: 0.8, 0.7

# OK（v11 形式）
quadrantChart
  点A: [0.8, 0.7]
```

### 複数チャート同時描画失敗
**症状**: 2つ目以降のチャートが描画されない・壊れる  
**原因**: Mermaid の初期化が競合する
```typescript
// NG
await Promise.all(elements.map(el => mermaid.run({ nodes: [el] })));

// OK（順番に処理）
for (const el of elements) {
  await mermaid.run({ nodes: [el] });
}
```

### SVG クリーンアップ漏れ
**症状**: エラー後に空の SVG が残り後続の描画に影響  
**解決策**: エラー時は必ずクリーンアップ
```typescript
try {
  await mermaid.run({ nodes: [element] });
} catch (e) {
  element.innerHTML = ''; // 必ずクリーンアップ
  console.error('Mermaid render failed:', e);
}
```

---

## Anthropic API 関連

### 529 Overloaded エラー
**症状**: API 呼び出しが `529` で失敗する  
**原因**: サーバー過負荷  
**解決策**: 指数バックオフリトライ
```typescript
async function callWithRetry<T>(fn: () => Promise<T>, maxRetries = 3): Promise<T> {
  for (let i = 0; i < maxRetries; i++) {
    try {
      return await fn();
    } catch (e: any) {
      if (e.status === 529 && i < maxRetries - 1) {
        await new Promise(r => setTimeout(r, 1000 * Math.pow(2, i)));
        continue;
      }
      throw e;
    }
  }
  throw new Error('Max retries exceeded');
}
```

```ruby
# Ruby 版
def call_with_retry(max_retries: 3)
  retries = 0
  begin
    yield
  rescue Anthropic::APIStatusError => e
    if e.status == 529 && retries < max_retries
      sleep(2 ** retries)
      retries += 1
      retry
    end
    raise
  end
end
```

---

## React / Next.js 関連

### Duplicate Key Warning
**症状**: `Warning: Each child in a list should have a unique "key" prop`  
**解決策**: 配列インデックスではなく一意な ID を使う
```tsx
// NG
items.map((item, i) => <Item key={i} {...item} />)

// OK
items.map((item) => <Item key={item.id} {...item} />)
```

---

## Rails 関連

### PostgreSQL 接続エラー
**症状**: `PG::ConnectionBad` / `connection pool is exhausted`  
**原因**: 接続プールの枯渇または `DATABASE_URL` 未設定  
**チェック項目**:
- 環境変数 `DATABASE_URL` が正しく設定されているか
- `config/database.yml` の `pool` 設定
- Render 環境では DB の接続上限に注意（Free プランは 97 接続まで）

# デザイン・アクセシビリティ ワークフロー（FDD）

## Figma-Driven Development フロー
1. **Figma で設計確認** → Figma MCP で `get_design_context` または `get_screenshot` を実行
2. **トンマナ・デザインシステム定義** → `python /Users/maedashiori/awesome-design-md/suggest_design.py` で DESIGN.md 生成
3. **Tailwind に反映** → DESIGN.md のカラー・タイポ・スペーシングを `tailwind.config` に追加
4. **コンポーネント実装** → DESIGN.md の仕様に沿って実装
5. **a11y 検証** → QA チェックリストの a11y 項目を確認

## awesome-design-md の使い方
```bash
cd /Users/maedashiori/awesome-design-md
python suggest_design.py  # 対話形式で DESIGN.md を生成
```
生成された DESIGN.md をプロジェクトルートに配置し、Claude への指示に含める。

---

## WCAG AA 基準チートシート
| 項目 | 基準値 |
|------|--------|
| テキストのコントラスト比 | 4.5:1 以上 |
| 大きなテキスト（18pt / 14pt bold 以上）| 3:1 以上 |
| フォーカスインジケーター | 見えること |
| タッチターゲット最小サイズ | 44×44px |

---

## よく使う ARIA パターン

### ダイアログ・モーダル
```tsx
<div role="dialog" aria-modal="true" aria-labelledby="dialog-title">
  <h2 id="dialog-title">タイトル</h2>
</div>
```

### ライブリージョン（通知・エラー）
```tsx
<div role="alert" aria-live="assertive">エラーメッセージ</div>
<div aria-live="polite">ステータス更新</div>
```

### アイコンのみのボタン
```tsx
<button aria-label="閉じる">
  <CloseIcon aria-hidden="true" />
</button>
```

### フォームエラー紐付け
```tsx
<input
  id="email"
  aria-describedby="email-error"
  aria-invalid={!!error}
/>
<p id="email-error" role="alert">{error}</p>
```

### スキップリンク（先頭に配置）
```tsx
<a href="#main-content" className="sr-only focus:not-sr-only">
  メインコンテンツへスキップ
</a>
```

---

## Tailwind a11y ユーティリティ
```tsx
// スクリーンリーダー専用テキスト
<span className="sr-only">補足テキスト</span>

// フォーカス時のみ表示
<a className="sr-only focus:not-sr-only focus:absolute focus:z-50">

// フォーカスリング強調
<button className="focus:outline-none focus-visible:ring-2 focus-visible:ring-blue-500">
```

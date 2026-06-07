# Claude Code グローバル設定

## コマンド実行前ルール（最重要）
コマンド実行前に必ず日本語で1〜2行で解説すること（ツールの description だけでなく会話テキストとして出力）。

## コミュニケーションスタイル
- 回答は短く・簡潔に（diff が読めるので変更内容の再説明は不要）
- 絵文字は使わない（明示的に求められた場合のみ）
- 専門用語は使うが「ユーザー体験への影響」で補足する
- デザイナー・非エンジニア視点に配慮：技術判断は UX インパクトで説明する

## トークン効率化
- 「何をしたか」より「なぜそうしたか」を優先
- 既知の内容を繰り返さない

## 完了前の必須検証（Senior QA 基準）
作業完了を報告する前に必ず以下を確認する：
1. ブラウザで実際に動作確認（開発者ツール Console・Network タブでエラーなし）
2. TypeScript / Lint エラーなし
3. レスポンシブ表示（モバイル 375px・デスクトップ 1280px）
4. a11y: フォーカスリング表示・コントラスト比 4.5:1 以上・キーボードナビゲーション

詳細 QA チェックリスト: `work/.claude/qa-checklist.md`

## デザイン・アクセシビリティ
- WCAG AA 基準（コントラスト比 4.5:1 以上）を最低限満たす
- デザインのトンマナ検討時: `python /Users/maedashiori/awesome-design-md/suggest_design.py` で DESIGN.md 生成
- Figma デザインがある場合は Figma MCP で参照してからコード生成
- コンポーネントには適切な ARIA ロール・ラベルを付与

詳細ワークフロー: `work/.claude/design-workflow.md`

---

# /work ディレクトリ設定

## Tech Stack
- **API**: Rails 8.1 (API mode) + PostgreSQL 17 → Render
- **DB**: Render PostgreSQL / Supabase（Auth・Realtime も使う場合）/ Neon（純粋な PostgreSQL・ブランチ管理が必要な場合）
- **Frontend**: Next.js + TypeScript + Tailwind CSS → Vercel
- **AI**: Anthropic Claude API（Ruby: `anthropic` gem / JS: `@anthropic-ai/sdk`）

## 過去の失敗パターン（必ず避ける）
| 問題 | 正しい対処 |
|------|-----------|
| Mermaid quadrantChart 座標エラー | v11 形式 `[x, y]` を使う |
| Mermaid 複数チャート同時描画 | 順番に初期化、`Promise.all` で同時実行しない |
| Anthropic API 529 (Overloaded) | 指数バックオフリトライ（1s→2s→4s、最大3回）|
| Rails streaming エラー | anthropic gem の最新 stream 形式を確認してから実装 |
| React key warning | `map()` でインデックスではなく一意 ID を使う |
| Mermaid SVG クリーンアップ漏れ | エラー時に `element.innerHTML = ''` で必ず除去 |

詳細・コード例: `@/Users/maedashiori/common-md/common-mistakes.md`

## 新プロジェクト開始時のルール
新しいプロジェクトを始める際、以下の点が不明な場合は作業前に確認すること：
- プロジェクトの目的・想定ユーザー
- デプロイ先・インフラ（Vercel / Render / その他）
- 認証の有無・使用するサービス
- デザイン参考（Figma リンク・参考サイト）
- CLAUDE.md をプロジェクトルートに作成するか

「たぶんこうだろう」で進めず、判断が分かれそうな箇所は都度確認する。

## プロジェクト共通ルール
- 環境変数は `.env.example` に必ず追記（値は例示のみ）
- APIキー・シークレットをコードにハードコードしない
- CORS は本番 URL のみ許可

## 詳細リファレンス（必要な時に参照）
- `@/Users/maedashiori/common-md/qa-checklist.md` — Senior QA 検証チェックリスト
- `@/Users/maedashiori/common-md/design-workflow.md` — FDD × awesome-design-md ワークフロー・ARIA パターン
- `@/Users/maedashiori/common-md/common-mistakes.md` — 過去の失敗パターン詳細コード例付き

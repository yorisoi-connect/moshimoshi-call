# もしもしコール ウェブサイト — CLAUDE.md

## プロジェクト概要

**サービス名**: もしもしコール  
**運営者**: よりそいコネクト  
**内容**: 一人暮らしの高齢者に週1回・約15分の定期電話でお話し相手になるサービス。通話後にご家族へレポートを送付。  
**本番URL**: `https://yorisoi-connect.com/moshimoshi-call/`  
**開発サーバー**: `python3 -m http.server 8123` を `/home/tokaji/workspace/` で起動 → `http://127.0.0.1:8123/moshimoshi-call/`

---

## 技術スタック

- **純粋な HTML5 / CSS3 / Vanilla JS のみ**（フレームワーク・ビルドツール一切なし）
- フォント: Google Fonts — Noto Sans JP (400, 700)
- アイコン・画像: `assets/icons/`、`assets/images/`
- JS機能: IntersectionObserver（フェードイン・マーカーアニメーション）、FAQアコーディオン、ハンバーガーメニュー、ブログカテゴリフィルター

---

## ファイル構成

```
moshimoshi-call/
├── index.html          # トップページ（メイン）
├── style.css           # 全ページ共通スタイル（単一ファイル）
├── script.js           # 全ページ共通スクリプト（単一ファイル）
├── price/index.html    # 料金プランページ
├── faq/index.html      # よくある質問ページ
├── blog/index.html     # ブログ一覧ページ
├── contact/index.html  # お問い合わせページ
├── company/index.html  # 会社概要ページ
└── assets/
    ├── icons/          # 機能アイコン（PNG）
    └── images/         # 画像・キャラクター（PNG）
        ├── hero.png
        └── tsunagu_tatie1.png  # イメージキャラクター（クリーム色の犬）
```

---

## カラーパレット（CSS変数）

```css
--color-primary: #4CAF72        /* メイングリーン */
--color-primary-dark: #3d9560   /* グリーン（濃） */
--color-accent: #F5A623         /* アクセントオレンジ */
--color-accent-dark: #d98e0f    /* オレンジ（濃） */
--color-bg-green: #F0FAF4       /* 背景：薄いグリーン */
--color-text: #333333
--color-text-light: #666666
--color-line: #06C755           /* LINEブランドカラー */
--color-border: #e0e8e2
--radius-card: 16px
```

---

## index.html セクション構成（順番を変えないこと）

| # | セクションID | 背景 | 内容 |
|---|---|---|---|
| 1 | hero | — | ヒーロー |
| 2 | worries | white | 課題提起（共感） |
| 3 | features | section-green | サービス紹介 |
| 4 | value | white | もしもしコールの価値 |
| 5 | comparison | section-green | 他サービスとの比較 |
| 6 | qol | white | 定期的な会話とQOL |
| 7 | report | section-green | レポート紹介 |
| 8 | price | white | 料金プラン（概要） |
| 9 | flow | section-green | ご利用の流れ |
| 10 | voice | white | 利用者の声 |
| 11 | faq | section-green | よくある質問（抜粋） |
| 12 | contact | white | お問い合わせCTA |

背景はwhite / section-green で交互になるよう維持すること。

---

## 実装方針・設計ルール

### リンクのパス
内部リンクはすべて `/moshimoshi-call/` から始まる絶対パスを使用する（相対パスは使わない）。

### CSSの構成
`style.css` は単一ファイル。セクション番号付きコメントで管理。新しいコンポーネントのスタイルは対応するセクションコメントの下に追加する。

### レスポンシブ
- `768px` 以下：タブレット・スマホ共通（ハンバーガーメニュー表示）
- `480px` 以下：スマホ特化（文字サイズ縮小、縦並びレイアウト）
- モバイル固定CTAバー（LINE + 電話）は768px以下で表示

### コンテナ幅
- `.container`：通常コンテンツ（`max-width: 1100px`）
- `.container-narrow`：狭めのコンテンツ（`max-width: 720px`）
- セクション内で幅を制限したい場合は内側のdivに `max-width + margin: 0 auto` を追加（`.qol-content`、`.value-columns` のパターン）

### スクロールアニメーション
- フェードイン: 要素に `.fade-in` クラスを付与 → JS の IntersectionObserver が `.is-visible` を追加
- マーカーアニメーション: `<span class="marker-text">テキスト</span>` → JS が `.is-marked` を追加してCSS transitionで左から右へ引かれる

### イメージキャラクター（吹き出しデザイン）
`assets/images/tsunagu_tatie1.png`（クリーム色の犬、幅120px・高さ160px、顔は上半分に位置）を使うとき：
- `.character-speech` > `.character-speech-img` + `.speech-bubble` の構造
- `align-items: flex-start` で画像の顔横に吹き出しが来るようにする
- 吹き出しの三角は `::before`（外枠・グリーン）と `::after`（内側・白）の2層で実現
- スマホ（480px以下）では縦並び（キャラクター上・吹き出し下）に切り替え

### テーブル
- `border-collapse: collapse` を使う（`separate` は使わない、過去にレイアウト崩れが発生した）
- テーブル角丸なし（`border-radius: 0`）
- もしもしコール列のハイライト: `background: #E8F6EE`、見出し行は `var(--color-primary-dark)` 背景・白文字

### セクション背景の波形（ウェーブ）
`white` と `section-green` の境界は `::after` 疑似要素で波形装飾。`padding-bottom` を大きめにとっている。モバイルでは高さを縮小（`style.css` の波形セクション参照）。

---

## 料金プラン概要（2ヶ月お試し制度）

**2ヶ月お試し制度**（index.html Section 8 および price/index.html に記載）:
- Step 1: 1ヶ月目は完全無料でお試し
- Step 2: 気に入ったら本登録（登録料 8,000円税込）。2ヶ月目月額は無料
- Step 3: 3ヶ月目から通常プランスタート（ライト 3,500円/月 or スタンダード 5,900円/月）

**注意**: `price/index.html` の料金説明もこの制度に合わせて更新が必要（未対応の可能性あり）。

---

## 過去に発生したトラブル・禁止事項

- `border-collapse: separate` → テーブルレイアウト崩れ。**使用禁止**
- `position: relative; left: 50%; width: 100vw; transform: translateX(-50%)` による全幅帯 → レイアウト崩れ。**使用禁止**
- セクションの順番変更 → ユーザーが「元に戻して」と要求した過去あり。**順番変更は事前に確認する**
- 統計情報セクションはindex.htmlから削除済み。ブログ等の別ページに掲載予定（`.stats-cards` 等のCSSは残存）

---

## script.js の構成

1. ヘッダースクロール検知（`.is-scrolled` クラス付与）
2. ハンバーガーメニュー開閉
3. FAQアコーディオン（`max-height` アニメーション）
4. スクロールフェードイン（`.fade-in` → `.is-visible`、threshold: 0.15）
5. マーカーハイライト（`.marker-text` → `.is-marked`、threshold: 0.8、200ms遅延）
6. ブログカテゴリフィルター（`blog/index.html` 専用）

---

## 未対応・TODO

- `price/index.html`：2ヶ月お試し制度への内容更新（index.html は対応済み）
- OGP画像（`images/ogp.png`）：プレースホルダー、公開前に差し替え
- `contact/index.html`：Googleフォームの埋め込みURL（`YOUR_GOOGLE_FORM_URL`）を本番URLに差し替え
- `company/index.html`：代表者名・所在地・設立日のプレースホルダーを公開前に記入

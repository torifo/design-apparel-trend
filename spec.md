# LUEUR — design-apparel-trend Spec

**Status:** Approved  
**Author:** torifo  
**Created:** 2026-05-18  
**Updated:** 2026-05-18

---

## 1. Overview

### Problem Statement
ファッション好きの若い女性（20–23歳）は、ZOZOTOWNのような大型ECでは「発見感」を得られず、SNS経由で見つけたブランドを調べようとしてもウェブサイトのデザインがペルソナのテイストと乖離しており、ブランドへの共感が生まれないまま離脱している。

### Goal
K-beauty・Y2K・ガーリー編集誌の美学を体現した架空ブランド「LUEUR」のランディングページを実装し、デザイン研究として公開する。ペルソナ（trend）が「このブランドのものが欲しい」と感じるビジュアル言語を検証する。

### Non-Goals
- 実際のカート・決済機能
- CMS・データベース連携
- ユーザー認証
- 多言語対応（日本語のみで可）

### Background
- GitHub Pages（静的ホスティング）で公開
- LUEURは本デザイン研究のために作成した架空ブランドであり、実在のブランド・店舗・商品ではない
- `design-apparel-trend` リポジトリ、`design.apparel-trend.riumu.net` への独自ドメイン割り当てを将来予定
- 同シリーズ4作のうちの1作（street・vintage・minimalと対比させる設計）

---

## 2. User Stories

| ID | Persona | Want to | So that |
|----|---------|---------|---------|
| US-01 | trend（20–23歳女性） | 一目でブランドの世界観を感じたい | 「私のためのブランド」と即座に判断できる |
| US-02 | trend | 新着商品を気軽にブラウズしたい | 欲しいものが見つかったときすぐ反応できる |
| US-03 | trend | ブランドのストーリーに共感したい | 購入に踏み切る感情的根拠を得る |
| US-04 | trend | スマートフォンで快適に閲覧したい | TikTok→ブラウザ遷移後も離脱せず読める |

### Acceptance Criteria (EARS notation)

**US-01: ファーストビューでの世界観提示**
- WHEN ページが読み込まれた THEN ローダーアニメーション（ロゴ+バー）が1.4秒以内に完了し、ヒーローセクションが表示される
- WHEN ヒーローが表示された THEN ラベンダー/ピンク/ゴールドのカラーパレット・Cormorant Garamondフォントが視認できる
- WHEN ユーザーがヒーローを見た THEN ブランド名「LUEUR」とタグライン「Glow in your own light」が読める

**US-02: 商品ブラウズ**
- WHEN ユーザーが商品カードにホバーした THEN 画像が緩やかにズーム（scale 1.06）しピンクのオーバーレイが現れる
- WHEN ユーザーがスクロールして商品グリッドに達した THEN カードが順番にフェードイン（stagger 80ms）する
- WHEN モバイルで閲覧した THEN 商品グリッドは2カラムで表示される

**US-03: ブランドストーリー**
- WHEN ユーザーがブランドストーリーセクションに到達した THEN 写真とテキストが左右に配置されゴールドのアクセントラインが表示される
- WHEN ユーザーが読んだ THEN ブランドの思想（2〜3段落）が読める

**US-04: モバイル対応**
- WHEN 375px幅のデバイスで閲覧した THEN 全セクションが横スクロールなしで表示される
- WHEN ユーザーがナビゲーションリンクをタップした THEN 対象セクションへスムーズスクロールする

---

## 3. Functional Requirements

| ID | Requirement | Priority | Notes |
|----|-------------|----------|-------|
| FR-01 | ページロードアニメーション（ロゴ+プログレスバー） | P0 | 1.4秒でフェードアウト |
| FR-02 | フルスクリーンヒーローセクション（写真+タイポグラフィ+CTA） | P0 | picsum.photos使用 |
| FR-03 | 商品グリッド（4商品、不規則配置） | P0 | 非対称レイアウト |
| FR-04 | マーキーテキスト（無限ループ） | P1 | ホバーで一時停止 |
| FR-05 | ブランドストーリーセクション | P1 | 写真+テキスト2段組 |
| FR-06 | カテゴリセクション（2カラム大判） | P1 | TOPS / DRESSES |
| FR-07 | フッター（ニュースレター欄+リンク） | P2 | 機能不要、UIのみ |
| FR-08 | スクロールリビールアニメーション | P1 | IntersectionObserver |
| FR-09 | ナビゲーション（ロゴ中央、スクロールで背景変化） | P0 | |
| FR-10 | フィルムグレイン固定オーバーレイ | P1 | opacity 0.035 |
| FR-11 | モバイルファースト対応（375px基準） | P0 | |

---

## 4. Architecture

### Page Structure

```
index.html
├── <div class="loader">          # ロゴ + プログレスバー
├── <nav>                         # 中央ロゴ、左右リンク
├── <section class="hero">        # フルスクリーン写真 + タイポグラフィ
├── <section class="arrivals">    # 商品グリッド（4枚）
├── <div class="marquee">         # 無限スクロールテキスト
├── <section class="story">       # ブランドストーリー（写真+テキスト）
├── <section class="categories">  # 2カラムカテゴリ
└── <footer>                      # ニュースレター+リンク
```

### Component Responsibilities

| Component | Responsibility |
|-----------|---------------|
| Loader | 初回ロードのブランディング演出。1.4秒後に opacity:0 |
| Hero | ファーストインプレッション。写真+大型セリフ体+CTA |
| Arrivals grid | 商品4枚を非対称配置。ホバーエフェクト付き |
| Marquee | エネルギー補給。ブランドキーワードを流す |
| Story | テキストで共感を育てる。写真は感情的な補助 |
| Categories | 次のアクション誘導（TOPS / DRESSES） |
| Footer | サイト終端処理 + ニュースレター UI |

### Key Design Decisions

| Decision | Chosen | Rationale | Rejected alternatives |
|----------|--------|-----------|----------------------|
| テーマ | ライトモード（ラベンダー霞） | trendペルソナはパステル・明るい画面を好む | ダークモード（ARCHと被る） |
| Display font | Cormorant Garamond | フランス的エレガンスでガーリーを格上げ | Playfair（vintageに割当済）、Lora（地味） |
| Body font | Nunito | 丸みで親しみやすく、モバイル可読性高い | DM Sans（coldすぎる）、Poppins（汎用すぎる） |
| グリッド | 不規則（雑誌的） | 「発見感」を演出。整列は単調に見える | 厳格グリッド（FORMに割当済） |
| カーソル | カスタム（小円） | ガーリーな世界観強化 | デフォルト（体験が弱い） |
| Grain | あり（淡く） | フィルム質感でY2K感を出す | なし（ARCHほど主張しない） |

---

## 5. Design System

### Color Palette
```css
--bg:           #FAF7FF;   /* ラベンダー霞 */
--bg-sub:       #F3EEF8;   /* 薄パープル */
--bg-card:      #FFFFFF;
--fg:           #1A1520;   /* ディープパープル黒 */
--fg-muted:     #9B8FA8;   /* ミュートモーブ */
--border:       #E8E0F0;
--accent:       #E8C4D4;   /* 桜ピンク */
--accent-gold:  #C9A84C;   /* シャンパンゴールド */
--accent-deep:  #7B5EA7;   /* ラベンダーディープ */
```

### Typography
```css
--font-display: 'Cormorant Garamond', 'Noto Serif JP', serif;
--font-body:    'Nunito', 'Noto Sans JP', sans-serif;
```
- Google Fonts CDN から読み込み
- Display: 見出し・ロゴ・大型テキスト
- Body: 本文・UI テキスト

### Spacing & Motion
```css
--ease: cubic-bezier(0.25, 0.46, 0.45, 0.94);  /* 甘いイーズアウト */
/* fadeUp: 0.8s, stagger 80ms per card */
/* hero title: 1.1s slideUp, delay 1.3s */
```

---

## 9. Testing Strategy (Visual QA)

| Layer | Scenarios |
|-------|-----------|
| Desktop (1280px) | ヒーロー全幅表示、商品グリッド4カラム確認 |
| Mobile (375px) | 横スクロールなし、商品2カラム、タップターゲット44px以上 |
| アニメーション | ローダー1.4秒完了、スクロールリビール、マーキー停止 |
| ホバー | 商品カードズーム、カーソル変化 |
| フォント読み込み | Cormorant Garamond・Nunito 正常適用確認 |

---

## 10. Implementation Notes

- **画像**: `picsum.photos/seed/lueur{n}/{w}/{h}` — CSS で `filter: saturate(0.8) brightness(1.05)` 適用
- **Grain**: SVG `feTurbulence` を `position:fixed` の疑似要素で実装、`opacity:0.03`
- **マーキー**: コンテンツを2セット複製し `translateX(-50%)` アニメーションで無限ループ
- **スクロールリビール**: `IntersectionObserver` で `.reveal` クラスに `.in` を付与
- **ローダー**: `window.addEventListener('load', ...)` で 1400ms 後に `.gone` クラス付与
- **ナビ中央ロゴ**: `position:absolute; left:50%; transform:translateX(-50%)` で実現

---

## 11. Open Questions

| # | Question | Owner | Due | Status |
|---|----------|-------|-----|--------|
| 1 | カスタムカーソルのデザイン（円 or ハート）どちらが良いか | torifo | 実装時 | Open |
| 2 | Editorial Stripセクション（横スクロールコーデ）は実装するか | torifo | 実装時 | Open |
| 3 | `design.apparel-trend.riumu.net` のDNS設定タイミング | torifo | 後日 | Open |

---

## References

- [spec.md（ナビゲーター）](../spec.md)
- [ARCH spec（street）](../street/spec.md)
- Competitor: [Heather](https://cc-heather.com), [jouetie](https://jouetie.com)
- Font: [Cormorant Garamond](https://fonts.google.com/specimen/Cormorant+Garamond), [Nunito](https://fonts.google.com/specimen/Nunito)

---

spec.md is ready. A few questions before you hand this off:

1. Does the Problem Statement accurately describe the problem you're solving?
2. Are there any sections missing that you'd expect in a spec for this kind of feature?
3. Is the level of detail right — too sparse, too dense, or just right?
4. Anything about the structure or format you'd want different next time?

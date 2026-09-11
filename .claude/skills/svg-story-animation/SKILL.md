---
name: svg-story-animation
description: 意味のあるSVGストーリーテリング・アニメーション（animate / animateTransform / animateMotionによるネイティブSVGアニメーション）を設計・実装するためのスキル。ロゴやアイコンの単純な装飾ではなく、収束・因果関係・変化のプロセス・循環・成長・分岐・段階的な進行といった「概念」を視覚的に説明するアニメーションを作るときに使う。スライド、インフォグラフィック、ランディングページ、ダッシュボードなど、SVGで何かの動き・変化・関係性を表現したい場面では、明示的に「SVG」や「animate」と言われていなくても積極的に使うこと。「意味のあるアニメーション」「プロセスを見せたい」「変化を表現したい」も十分なトリガーになる。
---

# SVGストーリーテリング・アニメーション

このスキルは、SVGアニメーションを「装飾」ではなく「説明」として使うための設計指針とリファレンス集です。特定のスライド生成ワークフローには依存しません（疎結合）。HTMLプレゼンテーション、Webサイト、ダッシュボードなど、SVGアニメーションが必要などんな場面からでも単独で使えます。

参照元：
- ICS MEDIA「SVGアニメーションの作り方」（https://ics.media/entry/15970/） — CSS/SMIL/JS/After Effectsの使い分け
- supermemoryai/skills の `svg-animations`（https://github.com/supermemoryai/skills/blob/main/svg-animations/SKILL.md, MIT License）— SVG基礎・SMIL構文・ベストプラクティスの体系化
- Skillselion「Best SVG animation skills for Claude Code」（https://skillselion.com/guides/best-svg-animation-skills-for-claude-code）— 公開されているSVG/アニメーション系skillの比較。上記 `svg-animations`（supermemoryai）が最多導入数の第一候補として挙げられており、本スキルもこれを主参照にした選定の裏付けとした
- Adsights「SVG Animation in 2026: Capabilities, Trade-offs, and Best Practices」（https://www.adsights.ai/blog/topics/creative-strategy/svg-animation-capabilities-and-best-practices）— フィルタのパフォーマンス特性、グループ単位の振り付け、`getTotalLength()`の使いどころ
- mamutlove「Masks, gradients and SMIL animations on an SVG」（https://mamutlove.com/en/blog/masks-gradients-and-animations-smil-on-svg/）— グラデーションのstop offsetやマスク要素をSMILで直接動かす手法
- Codrops「Creative Gooey Effects」（https://tympanus.net/codrops/2015/03/10/creative-gooey-effects/）— `feGaussianBlur`+`feColorMatrix`+`feComposite`によるグーイー（有機的に融合する）フィルタ
- MDN「CSS scroll-driven animations」（https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_scroll-driven_animations）— `animation-timeline: scroll()/view()` によるスクロール連動アニメーション（2026年時点で主要ブラウザの約84%が対応）
- Frontend Masters「Morphing Arbitrary Paths in SVG」（https://frontendmasters.com/blog/morphing-arbitrary-paths-in-svg/）— 頂点数の異なる形状同士のモーフィングと、flubber / GSAP MorphSVGPluginの使い分け。GSAPは2025年5月にWebflowの買収を経てMorphSVG/DrawSVG/ScrollTriggerを含む全プラグインが無料化されている

これらの記事・skillの構成と要点を踏まえて、本プロジェクト向けに「意味を運ぶアニメーション」という観点を加えて再構成しています。

**関連する他候補（このプロジェクトでは未採用。用途が変わったら検討する）**：アイコンの組み立てアニメーションに特化した `svg-assembly-animator`、GSAPのタイムライン・スクロール連動が要る場合の `gsap-core`、React/Framer Motion環境での `motion-advanced`。本プロジェクトはフレームワーク非依存の単一HTMLが要件のため、追加ライブラリ不要なネイティブSVG（SMIL）を選んでいる。ネイティブでは構造的に無理がある場合の外部ライブラリの検討基準は [references/library-escape-hatch.md](references/library-escape-hatch.md) を参照。

## 作る前に：3つの問い

装飾的なアニメーションと説明的なアニメーションの違いは、作り始める前にこの3つに答えられるかどうかです。

1. **このアニメーションが説明している"仕組み"は何か？**（収束／因果／変換／循環／成長／分岐／段階的進行 のどれか、または別の具体的な仕組み）
2. **アニメーションを止めても（最終状態だけ見ても）、同じ結論を静止画として伝えられるか？**（伝えられないなら、アニメーションが情報を隠しているサインなので設計を見直す）
3. **主役は何個か？**（同時に動く要素は3〜4個まで。増えすぎたら、そもそも図を分けるべき）

「仕組み」が決まったら、それを見せる**手段**（動くパス上の点／フィルタの発光／グラデーションの流れ／形状のモーフィング／領域のワイプ／viewBoxのズーム、など）は複数あります。同じ仕組みでも毎回同じ手段を選ぶと似たアニメーションばかりになるので、[references/storytelling-patterns.md](references/storytelling-patterns.md) 冒頭の「概念→パターン早見表」で複数の候補から選び、同一デッキ内で同じパターン番号を3回以上使わないようにする。

## 技術選定：CSS / SMIL / JS のどれを使うか

ICS MEDIAの整理に沿って使い分けます。

| 手法 | 得意なこと | 向いている場面 |
|---|---|---|
| **CSS**（`transition` / `@keyframes`） | 塗り・線・拡縮・回転・移動などCSSプロパティで表現できる変化。ホバーやクラス切り替えとの相性が良い | ボタンやアイコンの状態変化、スライドの背景演出など「副次的な」動き |
| **SMIL**（`<animate>` / `<animateTransform>` / `<animateMotion>`） | SVGマークアップ内で完結する自己再生アニメーション。`<img>`タグやCSS背景画像としても動く | このスキルが主に対象とする「意味を説明する中心アニメーション」。外部JSなしで完結させたいとき |
| **JavaScript**（Web Animations API等） | ユーザー操作やアプリの状態に応じて値・タイミングを動的に変える。`pause()` / `reverse()` などの再生制御 | クリックで進むプロセス図、データに応じて変わるアニメーションなど、インタラクションが要件に入るとき |
| **CSS Scroll-driven Animations**（`animation-timeline: scroll()` / `view()`） | スクロール量・要素の可視領域進入をそのままアニメーション進行度にマッピングする。メインスレッドが重くてもコンポジタ側で滑らかに動く | ページ自体がスクロールするランディングページ・ダッシュボードでの「読み進めると図が完成する」演出。`slide-deck-html` の固定サイズステージのようにページがスクロールしない構成には不向き |

**方針**：中心となる「意味のあるアニメーション」はまずSMILで自己完結させる。インタラクション（クリックで次のステップへ、など）が要件にあるときだけJSを併用する。ボタンのホバーなど付随的な演出はCSSに任せ、SMILの責務を汚さない。ページ全体がスクロールする構成（スライドの固定ステージではない）なら、CSS Scroll-driven Animationsで「スクロール＝進行度」を直接結びつける選択肢も検討する。

## リファレンス

- [references/fundamentals.md](references/fundamentals.md) — 座標系・パスコマンド・`<defs>` / グラデーション / マスク、フィルタ（`feGaussianBlur` / `feTurbulence` / `feDisplacementMap` / グーイー合成）、テキストオンパス、CSS Scroll-driven Animations、モーフィングの制約と対処、SMILのタイミング属性一覧、イージング（`calcMode="spline"` の基本形とオーバーシュート/バネ的な近似）。実装前に迷ったらまずここ。
- [references/storytelling-patterns.md](references/storytelling-patterns.md) — 冒頭の「概念→パターン早見表」で仕組みと視覚表現の組み合わせを一覧できる、30パターンのコピペ可能なSVGスニペット集（動くパス上の点、発光・グーイー融合・液体の満ち・放射状バースト・ワイプ／リビール・グラデーションシマー・テキストオンパス追従・比較バーレース・ネットワーク点灯・viewBoxズーム・弾性バウンドに加え、分解→組立・ドミノ連鎖・経路描画＋カメラパン・樹形成長・鼓動／波形・織り込み・結晶化・ワイプ比較・キネティックタイポグラフィ・データグロースまで収録）。まず「作る前の3つの問い」で仕組みを特定し、早見表から対応するパターンを開く。1シーンで足りない場合は末尾の「複合シーン」で複数パターンを時間軸でつなぐ。
- [references/illustration-styles.md](references/illustration-styles.md) — アニメーションを止めた「静止画そのもの」の絵づくり指針。汎用ストック人物イラスト（unDraw等）に頼らない6つの語彙（幾何学的抽象／データ・アズ・アート／タイポグラフィ／アイソメトリック技術図解／カスタムモノラインアイコン／テクスチャ）と、装飾要素をデッキ全体で使い回さないための一貫性ルール。
- [references/library-escape-hatch.md](references/library-escape-hatch.md) — ネイティブSVG/CSSで構造的に無理がある場合（頂点数が大きく異なるモーフィング、本格的な物理バネ、複雑なスクロール演出）にだけ外部ライブラリ（GSAP / flubber等）を検討する基準と、単一HTML要件を壊さないための埋め込み方。
- [references/checklist.md](references/checklist.md) — 実装後に見るチェックリスト（アクセシビリティ、パフォーマンス、パスモーフィングの制約、パターン・イラスト意匠の偏りなど）。

## このスキルが担当しないこと

- ページ全体のレイアウト、ナビゲーション、進捗バーなどの「入れ物」の設計 → `slide-deck-html` スキルの領分です。このスキルは各スライド・各セクションの「中心アニメーション1個」の設計だけに責任を持ちます。
- PowerPoint/PDF変換、デプロイなどの配布まわり → 対象外。

---
name: slide-deck-html
description: 自己完結した1つのHTMLファイルで、クリック・矢印キー・前後ボタンで操作できるプレゼンテーション（スライドデッキ）を作るスキル。固定サイズのステージ、進捗バー、ページ番号、キーボード/クリック/ボタンのナビゲーションを備えたビルド済みの土台（シェル）と、「タイトルに主張を書く」「1ページ1メッセージ」「レイアウトの型（ヒーロー／ステートメント／統計ヒーロー／比較／引用／フルブリード／クアドラント／ディバイダー等）を混ぜて単調な反復を避ける」「タイポグラフィ・余白・配色のデザイントークンで統一感を出す」「AI臭（文章・見た目とも）を消す」「フレッシュアイ・レビューを通す」といった構成規約を提供する。ユーザーが「発表スライド」「プレゼン」「スライド資料」をHTML/Web形式で求めているとき、あるいは既存デッキが「同じテンプレの繰り返しで単調」「もっとデザイン性を高めたい」と言っているときも、スキルやSVGという言葉が出てこなくても使うこと。各スライドの中心的なアニメーション表現そのものは `svg-story-animation` スキルに委ねる（疎結合）。
---

# HTMLスライドデッキ

このスキルは「入れ物」（固定ステージ・ナビゲーション・進捗表示・構成規約・仕上げのレビュー工程）を担当します。各スライドの中心に置く「意味のあるアニメーション」の作り方は、姉妹スキルの **`svg-story-animation`** が担当します。両者は疎結合で、このスキルはSVGスニペットを持たず、`svg-story-animation` は特定のスライド構造を前提にしません。スライド以外（例：単発のインフォグラフィック）にアニメーションが要る場合は `svg-story-animation` 単体で使えます。

参照元：
- `carnot-tech/consulting-pptx-skill`（MIT License, github.com/carnot-tech/consulting-pptx-skill）— 実務レビュー由来・約80項目の「スライド作成ルール正典」、AI臭ワード集、フレッシュアイ・レビューという仕上げ工程。PPTX／印刷前提の規約なので、このプロジェクトの単一HTML形式向けに要点を翻案して [references/slide-rules.md](references/slide-rules.md) と [references/review-prompt.md](references/review-prompt.md) を作成した
- `zarazhangrui/frontend-slides`（MIT License, github.com/zarazhangrui/frontend-slides）— 固定16:9ステージとナビゲーションコントローラーの実装。[references/stage-and-navigation.md](references/stage-and-navigation.md) に1600×900へ翻案して掲載し、元の実装は `references/vendored/` に同梱
- 実成果物（`output/`配下）のレビューから得た知見 — 全ページが同一テンプレートの反復になる・汎用ストックイラストで量産感が出るという課題を踏まえ、[references/design-system.md](references/design-system.md)（タイポグラフィ・余白・配色トークン）、[references/layout-gallery.md](references/layout-gallery.md)（11種のレイアウトアーキタイプ）、[references/visual-slop-lexicon.md](references/visual-slop-lexicon.md)（見た目のAI臭語彙集）を追加した

## ワークフロー

`consulting-pptx-skill` の「着手前に定義する→ストーリー設計→型の適応→機械チェック→フレッシュアイ・レビュー」という流れを、1回限りの依頼にも使える軽量版として採用します。

1. **着手前に定義する** — 何のための資料か（誰に・どういう場で・何を伝えるか）、講演で使うか配布して読ませるか（下記「密度モードの確認」）を先に決める。ブランドカラーやフォントの指定があれば[references/design-system.md](references/design-system.md)のトークンに落とし込む。自明な依頼はここを省略してよい。
2. **ストーリー設計** — 各ページの主張を1行で書き出す（[references/slide-rules.md](references/slide-rules.md) §1）。伝えたいことが1行で書けないページは、まだ設計が終わっていない。タイトルだけを並べて通し読みし、1本のストーリーになっているか確認する。
3. **型と仕組みの選定** — 各ページについて、主張の性質に合う**レイアウトの型**を[references/layout-gallery.md](references/layout-gallery.md)から選ぶ（同じ型を3ページ以上連続させない）。あわせて、中心アニメーションの仕組みを `svg-story-animation` スキルの「作る前の3つの問い」で決める。データを見せるページは `data-graphics` の使い分け表（表／グラフ／ダイアグラム／タイムライン）も併用する。
4. **組み立て** — [references/stage-and-navigation.md](references/stage-and-navigation.md) の固定ステージ・コントローラーの型に、ステップ3で選んだレイアウト・アニメーション・図・本文を流し込む。人物イラストを使う場合は `svg-story-animation` の[illustration-styles.md](../svg-story-animation/references/illustration-styles.md)で汎用ストック感を避ける。
5. **機械的チェック＋AI臭の除去** — [references/slide-rules.md](references/slide-rules.md) §4のチェックリストを確認し、[references/vendored/ai-smell-lexicon.md](references/vendored/ai-smell-lexicon.md) で文章を、[references/visual-slop-lexicon.md](references/visual-slop-lexicon.md) で見た目のテンプレ感を見直す。可能ならヘッドレスブラウザで実レンダリングし、はみ出し・重なりをスクリーンショットで確認する。
6. **フレッシュアイ・レビュー** — 一定の分量・重要度がある資料では、[references/review-prompt.md](references/review-prompt.md) の指示文を使って、作り方を伏せた別のエージェント（新規セッション等）にデッキを読ませ、指摘を採否表にしてから反映する。数枚程度の簡単な依頼では省略してよい。
7. **レビューの反映** — ユーザーから直しが入ったら、その指摘を一般化できる範囲で [references/slide-rules.md](references/slide-rules.md) に1行追記する。同じ指摘を次回も口頭で繰り返させない。

## スライド作成規約（要約。詳細は references/slide-rules.md）

- **タイトルに主張を書く**。「背景・課題」のような分類名だけで終わらせず、「選択肢が増えるほど、進むべき道は見えにくくなる。」のように、そのページの結論を常体の一文で書く。
- **1スライド1メッセージ**。伝えたいことが2つあるなら2ページに分ける。
- **レイアウトの型を混ぜる**。全ページを同じ箱に流し込まない。[references/layout-gallery.md](references/layout-gallery.md)から主張の性質に合う型を選び、単調な反復を避ける。
- **図と文章を一致させる**。中心のSVGアニメーションが表現している仕組みと、タイトル・本文の主張が食い違っていないか必ず照合する。
- **AI臭を消す**。文章は、ですます調・空虚な強調・カタカナ盛り・名詞句化ビジネス語を書いた後に削る（[references/vendored/ai-smell-lexicon.md](references/vendored/ai-smell-lexicon.md)）。見た目は、汎用ストックイラスト・同一テンプレの反復・装飾の使い回しを削る（[references/visual-slop-lexicon.md](references/visual-slop-lexicon.md)）。
- **仕上げにフレッシュアイ・レビューを通す**。自分（＝作った本人）には見えない破綻は、条件を伏せた別エージェントに読ませて拾う。

## 固定ステージとナビゲーション（要約。詳細は references/stage-and-navigation.md）

- スライドは固定ピクセルサイズのステージ（本プロジェクトの既定は1600×900、16:9）で作成し、画面全体には1つのJS transform（`translate + scale`）で拡大縮小する。レスポンシブブレイクポイントで中身を組み替えない。
- スライドの表示/非表示は `opacity` / `visibility` / `pointer-events` で切り替える。`display` の切り替えは、後から追加したレイアウト用CSS（例：`.slide-content{display:flex}`）に上書きされて全スライドが同時に表示される事故が起きるので使わない。
- ナビゲーション（クリック・矢印キー・前後ボタン）と進捗バー・ページ番号は、固定ステージの外側（実ビューポート基準）に配置する。
- [references/vendored/](references/vendored/) には出典（frontend-slides / consulting-pptx-skill）を明記した実物ファイルを同梱している。CSS演出用の `animation-patterns.md` は副次的な動き（ボタンのホバー、スライド間フェード等）にのみ使い、中心アニメーションの代用にはしない（それは `svg-story-animation` の役割）。
- タイポグラフィ・余白・配色は都度page単位で決め直さず、[references/design-system.md](references/design-system.md)のトークン（type scale, spacing scale, color roles）をCSS変数として最初に定義し、全ページ・全スキルの図表がそこから値を引く。

## 密度モードの確認

作り始める前に、これが「講演で使う（少ない文字・大きい図）」ものか「配布して読ませる（自己完結・やや情報量多め）」ものかをユーザーに確認する。決まらない場合は、ライブでの説得が目的なら低密度、非同期の回覧・詳細確認が目的なら高密度をデフォルトにする。

# 同梱ファイルの出典

このフォルダのファイルは、いずれもMITライセンスの公開スキル／リポジトリから、出典を明記した上でそのまま、または一部改変して同梱しています。

## 1. frontend-slides（Zara Zhang）

https://github.com/zarazhangrui/frontend-slides （MIT License。全文は `LICENSE-frontend-slides`）

| ファイル | 由来 | 備考 |
|---|---|---|
| `viewport-base.css` | `frontend-slides/viewport-base.css` | 固定ステージ用CSS。本プロジェクトでは1600×900のステージを使うため、このファイル中の`1920px`/`1080px`はあくまで参考値。実際のステージCSSは [../stage-and-navigation.md](../stage-and-navigation.md) に1600×900へ翻案済みのものを掲載している。 |
| `animation-patterns.md` | `frontend-slides/animation-patterns.md` | CSSによる入場演出・背景演出・効果と雰囲気の対応表。スライドの**中心的な意味を運ぶアニメーション**の代わりには使わない（それは姉妹スキル `svg-story-animation` の役割）。ボタンのホバーやスライド間フェードなど、副次的な演出にのみ使う。 |

## 2. consulting-pptx-skill（Carnot AI Inc.）

https://github.com/carnot-tech/consulting-pptx-skill （MIT License。全文は `LICENSE-consulting-pptx-skill`）

| ファイル | 由来 | 備考 |
|---|---|---|
| `ai-smell-lexicon.md` | `consulting-pptx-skill/references/ai-smell-lexicon.md` | AIっぽい日本語の語彙・言い回し・トーンを消すためのチェックリスト。ほぼそのまま採用（スライドに限らず汎用のため改変不要と判断）。 |

`consulting-pptx-skill` の中核である `references/slide-rules.md`（約80項目のスライド作成規約）は、PPTX／印刷・pt単位・表組みの書式など本プロジェクトの形式（1600×900固定ステージのアニメーション付き単一HTML）に合わない項目が多いため、そのまま同梱せず、本プロジェクト向けに要点を再構成したものを [../slide-rules.md](../slide-rules.md) として作成した。同様に `references/content-review-prompt.md`（フレッシュアイ・レビューの指示文）も、HTML/PDF/PPTX前提の記述をこのプロジェクトの単一HTML形式向けに書き直し、[../review-prompt.md](../review-prompt.md) とした。印刷品質の表・グラフ・PPTX変換が必要になった場合は、元リポジトリを直接参照するとよい。

## なぜ同梱するか

デッキは単一ファイルでオフラインでも完結させたいため、GitHubへの参照リンクだけでなく実体を同梱し、将来の編集時にも版が固定されたスニペットを参照できるようにしている。

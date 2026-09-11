# SVG基礎とSMIL構文リファレンス

supermemoryai/skills の `svg-animations`（MIT License）と ICS MEDIA の解説記事の要点を、本プロジェクト向けに再構成したものです。

## 座標系とパス

- `viewBox` は必須。`width` / `height` をハードコードすると解像度非依存性が失われる。
- パスコマンド（大文字＝絶対座標、小文字＝相対座標）：

| コマンド | 意味 |
|---|---|
| `M` / `m` | 移動（ペンを上げて次の開始点へ） |
| `L` / `l` | 直線 |
| `H` / `h` | 水平線 |
| `V` / `v` | 垂直線 |
| `C` / `c` | 3次ベジェ曲線 |
| `S` / `s` | スムーズな3次ベジェ（前の曲線の接線を引き継ぐ） |
| `Q` / `q` | 2次ベジェ曲線 |
| `A` / `a` | 楕円弧 |
| `Z` / `z` | パスを閉じる |

## `<defs>` と再利用

グラデーション・フィルタ・マスク・繰り返し使う図形は `<defs>` にまとめ、`<use href="#id">` や `fill="url(#id)"` で参照する。同じ図形を複数回描くときは複製せず `<use>` を使うとファイルサイズと保守性が改善する。

## SMILアニメーション要素

| 要素 | 用途 |
|---|---|
| `<animate>` | 任意の属性値をアニメーション（`fill` / `opacity` / `r` / `stroke-dashoffset` など） |
| `<animateTransform>` | `translate` / `rotate` / `scale` / `skewX` / `skewY` の変形 |
| `<animateMotion>` | `<mpath href="#path">` で指定したパスに沿って要素を移動。`rotate="auto"` で進行方向に自動で向きを合わせる |
| `<set>` | 補間なしで属性を離散的に切り替える（例：ある時刻でクラス相当の見た目を切り替える） |

### タイミング属性

```
begin="0s"              直後に開始
begin="click"           クリックで開始
begin="2s"              2秒後に開始
begin="other.end"       他のアニメーション終了と同時に開始
begin="other.end+1s"    他のアニメーション終了の1秒後
begin="other.repeat(2)" 他のアニメーションが2回目のリピートに入った瞬間
```

複数の要素を連鎖させたいときは、決め打ちの`begin`秒数を並べるより、`other.end` を使うと後から時間を調整しやすい。ただし本プロジェクトのようにループ全体を単純に見せたい場合は、可読性を優先して秒数指定のstaggerで十分なことも多い。

### イージング（`calcMode="spline"`）

`keyTimes` と `keySplines` を組み合わせるとcubic-bezier相当のイージングになる。

```xml
<animate attributeName="cx" values="0;100" keyTimes="0;1" dur="1s"
  calcMode="spline" keySplines="0.42 0 0.58 1"/>
```

代表的なプリセット：

| 名前 | keySplines |
|---|---|
| ease-in-out | `0.42 0 0.58 1` |
| ease-out | `0 0 0.58 1` |
| ease-in | `0.42 0 1 1` |

## CSSでSVGを動かす場合の要点

- `fill` / `stroke` / `opacity` / `transform` / `stroke-dasharray` / `stroke-dashoffset` などはCSSでもアニメーション可能。
- **線画描画（ストロークドローイング）**：`stroke-dasharray` にパス全長、`stroke-dashoffset` を全長→0にアニメーションさせると「自分で描かれる」効果になる。パス全長はJSの `element.getTotalLength()` で正確に取得できる（SMILで書く場合は概算値で十分なことが多い）。
- 対応ブラウザが新しめでよければ、`d` プロパティ自体をCSSでアニメーションできる（ただし前後の `d` は同じコマンド構造である必要がある＝下記シェイプモーフィングの制約と同じ）。
- **移動＋バウンド**：`offset-path` と easing関数 `linear(...)` を組み合わせると、パスに沿った移動の最後にバウンドするような表現ができる（CSS Easing Level 2）。SMILでは同等の効果を `keySplines` の多段指定や、着地後に別の `<animate>` を `begin="move.end"` で連結して表現する。

## シェイプモーフィングの制約

2つの `d` の間をアニメーションで補間する場合、**コマンドの数・種類・並び順が同じ**である必要がある（例：両方とも `M L C C C Z` の形）。異なる構造の間で無理に補間しようとすると破綻するので、あらかじめ両方の形を同じコマンド構成で描き直す。

## パフォーマンスとGPU最適化

- `transform` と `opacity` のアニメーションはブラウザがコンポジット層として最適化しやすい。位置やサイズを頻繁に変えるなら、`x`/`y`/`width`/`height` を直接アニメーションするより `transform: translate()` / `scale()` を優先する。
- 同時に動くDOM要素・アニメーションの数を増やしすぎない（3〜4個が目安）。

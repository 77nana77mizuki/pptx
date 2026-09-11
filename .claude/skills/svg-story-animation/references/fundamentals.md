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

## シェイプモーフィングの制約と対処

2つの `d` の間をアニメーションで補間する場合、**コマンドの数・種類・並び順が同じ**である必要がある（例：両方とも `M L C C C Z` の形）。異なる構造の間で無理に補間しようとすると破綻するので、あらかじめ両方の形を同じコマンド構成で描き直す。

対処の優先順位：

1. **まず手で合わせる**：頂点が少ない側に、見た目に影響しない位置へ不可視の中間点（同じ座標に潰れた制御点など）を追加し、コマンド数を揃える。ほとんどのアイコン間モーフィングはこれで足りる。
2. **それでも構造差が大きすぎるとき**（穴のある形状、サブパス数が違う、頂点数が2倍以上違うなど）は、ネイティブでの補間を諦めて [references/library-escape-hatch.md](library-escape-hatch.md) のflubber/GSAP MorphSVGPluginを検討する。無理に手作業で合わせようとすると座標が不自然にねじれる。

## テキストオンパス

`<textPath>` で文字列をパスに沿わせられる。`startOffset` を `<animate>` で動かすと、ラベルやカウンタがルート上を移動しながら追従する表現になる。

```xml
<path id="routePath" d="M 80,500 Q 400,500 400,300 T 720,100" fill="none" stroke="var(--line)"/>
<text font-size="16" fill="var(--ink)">
  <textPath href="#routePath" startOffset="0%">
    <animate attributeName="startOffset" values="0%;100%" dur="4s" repeatCount="indefinite"/>
    現在地
  </textPath>
</text>
```

ロードマップの「今ここ」ラベルや、指標が経路を進むダッシュボードの注釈に向く。

## グラデーション・マスクをSMILで直接動かす

グラデーションやマスクの中身は静的な塗りとして扱われがちだが、`<stop>` や、マスク内の図形も他の要素と同じく `<animate>` の対象にできる。

**グラデーションの色が流れる**（`<stop>` の `offset` を動かす）：

```xml
<linearGradient id="flow">
  <stop offset="0.5" stop-color="var(--blue)">
    <animate attributeName="offset" values="0.5;0.1;0.5" dur="6s" repeatCount="indefinite" calcMode="paced"/>
  </stop>
  <stop offset="0.95" stop-color="var(--aqua)">
    <animate attributeName="offset" values="0.95;0.99;0.95" dur="6s" repeatCount="indefinite" calcMode="paced"/>
  </stop>
</linearGradient>
```

**グラデーションが帯として掃過する（シマー）**：`gradientTransform` 自体をアニメーションさせる。個々の `<stop>` ではなく `<linearGradient>` 要素の直下に置く。

```xml
<linearGradient id="shimmer" x1="0" y1="0" x2="1" y2="0">
  <stop offset="0" stop-color="var(--gold)" stop-opacity="0"/>
  <stop offset="0.5" stop-color="var(--gold)" stop-opacity="0.9"/>
  <stop offset="1" stop-color="var(--gold)" stop-opacity="0"/>
  <animateTransform attributeName="gradientTransform" type="translate"
    values="-1 0;1 0" dur="2.4s" repeatCount="indefinite"/>
</linearGradient>
```

**マスク内の図形を動かす**：マスクは「白く塗った部分だけ見える窓」なので、窓側の図形（円や矩形）を動かせば、下の図形はそのままに見え方だけが変化する。強調したい箇所へスポットライトを移動させる表現に使える。

## フィルタ効果（`<filter>`）

主なプリミティブ：

| プリミティブ | できること | 負荷 |
|---|---|---|
| `feGaussianBlur` | ぼかし | 半径依存で中〜重い |
| `feColorMatrix` | 色・アルファの変換 | 軽い |
| `feComposite` / `feMerge` | 複数レイヤーの合成・重ね書き | 軽い |
| `feOffset` | 位置をずらす（影の下敷きなどに） | 軽い |
| `feTurbulence` | Perlinノイズ生成（雲・大理石・紙質感） | 重い |
| `feDisplacementMap` | ノイズ等で形をゆがませる | 重い |

**発光（グロー）**：`feGaussianBlur` の結果と元画像を `feMerge` で重ねるのが定番。発光自体を毎フレーム変化させるとコストが高いので、**ぼかし量（`stdDeviation`）は固定し、動かすのは発光元の `r` / `opacity` / `scale`** にする。

```xml
<filter id="glow" x="-50%" y="-50%" width="200%" height="200%">
  <feGaussianBlur stdDeviation="6" result="blur"/>
  <feMerge>
    <feMergeNode in="blur"/>
    <feMergeNode in="SourceGraphic"/>
  </feMerge>
</filter>
```

**グーイー効果（有機的な融合）**：`feGaussianBlur` でぼかしてから `feColorMatrix` でアルファのコントラストを強調し、`feComposite` で元画像を重ねると、近づいた2つの図形が「くっついて」見える。統合・合意形成・合併のような概念に向く（出典：Codrops「Creative Gooey Effects」）。

```xml
<filter id="goo">
  <feGaussianBlur in="SourceGraphic" stdDeviation="8" result="blur"/>
  <feColorMatrix in="blur" mode="matrix"
    values="1 0 0 0 0  0 1 0 0 0  0 0 1 0 0  0 0 0 19 -9" result="goo"/>
  <feComposite in="SourceGraphic" in2="goo" operator="atop"/>
</filter>
```

**ノイズ・質感**：`feTurbulence` は単体でも紙・布・雲のような質感になり、`feDisplacementMap` と組み合わせると輪郭を有機的に揺らせる。`baseFrequency` や `seed` を `<animate>` すると常時ゆらぐが、非常に重いので使うのは1画面に1箇所、小さな要素に限定する。

## CSSスクロール連動アニメーション（`animation-timeline`）

`animation-timeline: scroll()` はスクロールコンテナの位置を、`animation-timeline: view()` は要素がビューポートに入ってから出るまでの割合を、そのままCSSアニメーションの進行度にマッピングする（2026年時点でChrome/Edge 115+, Firefox 132+, Safari 18+が対応、主要ブラウザの約84%）。メインスレッドが重くても表示側（コンポジタ）で処理されるため滑らかに動く。

```css
.draw {
  stroke-dasharray: 500;
  stroke-dashoffset: 500;
  animation: draw linear both;
  animation-timeline: view();
  animation-range: entry 10% cover 60%;
}
@keyframes draw { to { stroke-dashoffset: 0; } }
```

**適用条件**：これはページ自体がスクロールする構成（ランディングページ、レポート、ダッシュボード）向き。`slide-deck-html` のような固定サイズのステージ内では画面自体がスクロールしないため、この手法は使えない（その場合はSMILの `begin="click"` や自動再生ループで進行を作る）。

## イージングの拡張：オーバーシュート／バネ的な着地

`calcMode="spline"` の基本形（ease-in-out等）に加えて、`keyTimes` を細かく刻んで「行き過ぎてから戻る」区間を作ると、物理演算なしでバネ的な着地を近似できる。決定が着地する・結論に至る、といった場面の締めに向く。

```xml
<animate attributeName="cy" values="120;420;390;410;400" keyTimes="0;0.6;0.75;0.9;1"
  calcMode="spline"
  keySplines="0.3 0 0.7 1;0.3 0 0.7 1;0.3 0 0.7 1;0.3 0 0.7 1"
  dur="1.6s" fill="freeze"/>
```

（`values` の個数がNのとき、`keySplines` は N-1 個必要。上の例は5値なので4組。）

## パフォーマンスとGPU最適化

- `transform` と `opacity` のアニメーションはブラウザがコンポジット層として最適化しやすい。位置やサイズを頻繁に変えるなら、`x`/`y`/`width`/`height` を直接アニメーションするより `transform: translate()` / `scale()` を優先する。
- 同時に動くDOM要素・アニメーションの数を増やしすぎない（3〜4個が目安）。
- フィルタは `feGaussianBlur`（半径依存）・`feTurbulence`（重い）・`feDisplacementMap`（重い）の順に負荷が高い。これらの**属性値自体を毎フレーム変化させるのは避け**、発光やノイズは基本的に静的にしておき、動かすのは重ならない別属性（`opacity`/`transform`）にする。
- `filter` の適用範囲（`x`/`y`/`width`/`height`）を必要最小限に絞ると、再ラスタライズされる領域が減り軽くなる。

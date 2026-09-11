# ストーリーテリング・パターン集

「作る前の3つの問い」（SKILL.md参照）で仕組みを特定したら、対応するパターンを開いてコピーし、座標や色を調整する。原則ネイティブSVGの `<animate>` / `<animateTransform>` / `<animateMotion>` と `<filter>` / グラデーション / `clipPath` のSMILアニメーションのみで構成し、CSSキーフレームには頼らない（パターン19のみページ全体のスクロールが前提のCSS技法）。色は呼び出し元（スライド側など）で定義された変数（例：`var(--blue)`）を想定しているので、環境に合わせて置き換える。

## 概念→パターン早見表

同じ「仕組み」でも見せ方は複数ある。**同一デッキ内で同じ番号のパターンを3回以上使ったら、この表から別の候補に切り替える**——これが「毎回似たようなアニメーションになる」問題への直接的な対策になる。

| 仕組み・概念 | 候補パターン |
|---|---|
| 収束（複数が一点に集まる） | 1（収束）／13（グラデーションシマーで焦点を作る）／16（ネットワーク点灯が中心ノードに収束） |
| 気づき・因果の伝播 | 2（接続パルス）／16（ネットワーク点灯） |
| 変換・変化 | 3（データ→行動）／9（グーイー結合で2つが1つに）／18（モーフィングで形そのものが変わる） |
| 循環・フィードバック | 4（循環） |
| 成長・蓄積 | 5（成長ループ）／10（液体が満ちる）／17（viewBoxズームで詳細が育つ） |
| 分岐・拡大・展開 | 6（分岐拡大）／11（放射状バースト） |
| 段階的進行・ロードマップ | 7（段階的進行）／12（ワイプ／リビール）／14（テキストオンパス追従ラベル） |
| 比較・優劣・逆転 | 15（比較バーレース） |
| 強調・注目を集める | 8（グロー／パルス強調）／13（グラデーションシマー） |
| 決着・結論の着地 | 19（弾性バウンド） |
| ページスクロールに合わせた進行（ランディングページ等） | 20（スクロール連動プログレッシブ・リビール、CSS） |

## 1. 収束 — 複数の入力が一点・一本の道へ集まる

用途：複雑な状況が一つの意思決定に集約される、多様な意見が一つの方向にまとまる。

```svg
<path id="pth1" d="M 60,120 C 200,180 380,260 520,420" fill="none" stroke="var(--line)"/>
<circle r="6" fill="var(--blue-mid)">
  <animateMotion dur="3s" repeatCount="indefinite" keyPoints="0;1" keyTimes="0;1"
    calcMode="spline" keySplines="0.3 0 0.7 1">
    <mpath href="#pth1"/>
  </animateMotion>
</circle>
<!-- #pth2, #pth3 も同様に用意し、begin="0.4s" / "0.8s" などでずらして到着を重ねる -->
<circle cx="520" cy="420" r="10" fill="var(--yellow)">
  <animate attributeName="r" values="10;16;10" dur="1.2s" repeatCount="indefinite"/>
</circle>
```

## 2. 接続パルス — 注意・気づきが場面をつなぐ

用途：観察、気づき、「バラバラだったものがつながる」様子。

```svg
<circle cx="140" cy="300" r="8" fill="var(--aqua)">
  <animate attributeName="opacity" values="0.3;1;0.3" dur="2s" repeatCount="indefinite"/>
</circle>
<line x1="140" y1="300" x2="340" y2="260" stroke="var(--aqua)" stroke-width="2"
      stroke-dasharray="6 6" stroke-dashoffset="0">
  <animate attributeName="stroke-dashoffset" from="24" to="0" dur="1.5s"
    begin="0.3s" fill="freeze"/>
</line>
<circle cx="340" cy="260" r="8" fill="var(--aqua)">
  <animate attributeName="opacity" values="0.3;1;0.3" dur="2s" begin="0.6s" repeatCount="indefinite"/>
</circle>
```

## 3. データ→行動の変換 — 散らばった情報が一本の矢印になる

用途：解釈、「意味づけによって次の一手が見える」様子。

```svg
<g id="scatter">
  <circle cx="80" cy="60" r="4" fill="var(--ink-soft)"/>
  <circle cx="110" cy="95" r="4" fill="var(--ink-soft)"/>
  <circle cx="70" cy="120" r="4" fill="var(--ink-soft)"/>
  <animateTransform attributeName="transform" type="translate"
    values="0 0; 260 40; 260 40" keyTimes="0;0.6;1" dur="3s" repeatCount="indefinite"/>
</g>
<path d="M 360,100 L 620,100" stroke="var(--blue)" stroke-width="4"
      stroke-dasharray="260" stroke-dashoffset="260">
  <animate attributeName="stroke-dashoffset" values="260;260;0" keyTimes="0;0.6;1"
    dur="3s" repeatCount="indefinite"/>
</path>
<path d="M 605,88 L 622,100 L 605,112" fill="none" stroke="var(--blue)" stroke-width="4">
  <animate attributeName="opacity" values="0;0;1" keyTimes="0;0.6;1" dur="3s" repeatCount="indefinite"/>
</path>
```

## 4. 循環 — 情報がリング状のノードを巡る

用途：チーム・関係者間の情報循環、フィードバックが回り続ける様子。

```svg
<circle cx="400" cy="300" r="180" fill="none" stroke="var(--line)" stroke-width="2"/>
<path id="ringPath" d="M 580,300 A 180,180 0 1,1 220,300 A 180,180 0 1,1 580,300" fill="none" stroke="none"/>
<circle r="7" fill="var(--yellow)">
  <animateMotion dur="6s" repeatCount="indefinite" rotate="auto">
    <mpath href="#ringPath"/>
  </animateMotion>
</circle>
```

## 5. 成長ループ — 小さな輪が回るたびに大きくなる（PDCA・学習ループ）

用途：実験と学習を繰り返して確実に前進する様子。

```svg
<path id="spiral" fill="none" stroke="var(--blue-mid)" stroke-width="3"
  d="M 400,300 m 0,-20 a 20,20 0 1,1 0,40 a 40,40 0 1,1 0,-80 a 80,80 0 1,1 0,160"
  stroke-dasharray="600" stroke-dashoffset="600">
  <animate attributeName="stroke-dashoffset" from="600" to="0" dur="4s" fill="freeze"/>
</path>
<circle r="8" fill="var(--yellow)">
  <animateMotion dur="4s" repeatCount="indefinite">
    <mpath href="#spiral"/>
  </animateMotion>
</circle>
```

## 6. 分岐拡大 — 一つのノードから多方向へ広がる

用途：展開・スケール、一つの成功事例が複数拠点へ広がる様子。

```svg
<circle cx="200" cy="450" r="14" fill="var(--blue)"/>
<g>
  <path d="M 200,450 L 500,180" stroke="var(--line)" stroke-width="2" fill="none"
        stroke-dasharray="420" stroke-dashoffset="420">
    <animate attributeName="stroke-dashoffset" from="420" to="0" dur="1.2s" begin="0.2s" fill="freeze"/>
  </path>
  <circle cx="500" cy="180" r="8" fill="var(--aqua)" opacity="0">
    <animate attributeName="opacity" from="0" to="1" dur="0.3s" begin="1.4s" fill="freeze"/>
  </circle>
</g>
<!-- 終点座標とbeginの秒数を変えて同じgブロックを複製する -->
```

## 7. 段階的進行 — マーカーがマイルストーンを順番に進む

用途：ロードマップ、タイムライン。

```svg
<path id="road" d="M 100,700 L 400,700 L 700,700 L 1000,700 L 1300,700"
      stroke="var(--line)" stroke-width="4" fill="none"/>
<g>
  <circle cx="400" cy="700" r="10" fill="var(--blue-mid)"/>
  <circle cx="700" cy="700" r="10" fill="var(--blue-mid)"/>
  <circle cx="1000" cy="700" r="10" fill="var(--blue-mid)"/>
</g>
<circle r="14" fill="var(--yellow)">
  <animateMotion dur="5s" repeatCount="indefinite" keyPoints="0;0;0.33;0.33;0.66;0.66;1;1"
    keyTimes="0;0.1;0.32;0.42;0.64;0.74;0.96;1" calcMode="linear">
    <mpath href="#road"/>
  </animateMotion>
</circle>
```

## 8. グロー／パルス強調 — 静的な発光＋動く要素で注目を集める

用途：数値・結論・最重要ノードへの注目誘導。「これが答えです」の合図。

```svg
<defs>
  <filter id="glow" x="-50%" y="-50%" width="200%" height="200%">
    <feGaussianBlur stdDeviation="6" result="blur"/>
    <feMerge>
      <feMergeNode in="blur"/>
      <feMergeNode in="SourceGraphic"/>
    </feMerge>
  </filter>
</defs>
<circle cx="400" cy="300" r="24" fill="var(--yellow)" filter="url(#glow)">
  <animate attributeName="r" values="24;30;24" dur="2s" repeatCount="indefinite"/>
  <animate attributeName="opacity" values="0.85;1;0.85" dur="2s" repeatCount="indefinite"/>
</circle>
```

ぼかし量（`stdDeviation`）は固定し、動かすのは `r` / `opacity` だけにする（フィルタの再計算はコストが高い）。

## 9. グーイー結合 — 2つの要素が近づいて1つに融合する

用途：統合、合併、合意形成、「別々だったものが1つになる」瞬間。

```svg
<defs>
  <filter id="goo">
    <feGaussianBlur in="SourceGraphic" stdDeviation="8" result="blur"/>
    <feColorMatrix in="blur" mode="matrix"
      values="1 0 0 0 0  0 1 0 0 0  0 0 1 0 0  0 0 0 19 -9" result="goo"/>
    <feComposite in="SourceGraphic" in2="goo" operator="atop"/>
  </filter>
</defs>
<g filter="url(#goo)">
  <circle cx="260" cy="300" r="40" fill="var(--blue)">
    <animate attributeName="cx" values="260;380;380" keyTimes="0;0.6;1" dur="3s" repeatCount="indefinite"/>
  </circle>
  <circle cx="540" cy="300" r="40" fill="var(--blue)">
    <animate attributeName="cx" values="540;420;420" keyTimes="0;0.6;1" dur="3s" repeatCount="indefinite"/>
  </circle>
</g>
```

## 10. 液体が満ちる — クリップした容器の中で波打つ塗りが上昇する

用途：達成度の蓄積、目標に向けた充填、「満ちていく」成長の質感。

```svg
<clipPath id="containerClip">
  <rect x="300" y="150" width="200" height="300" rx="12"/>
</clipPath>
<rect x="300" y="150" width="200" height="300" rx="12" fill="none" stroke="var(--line)" stroke-width="2"/>
<g clip-path="url(#containerClip)">
  <path fill="var(--aqua)" opacity="0.85">
    <animate attributeName="d" dur="1.8s" repeatCount="indefinite"
      values="
        M300,420 Q350,405 400,420 T500,420 V450 H300 Z;
        M300,410 Q350,425 400,410 T500,410 V450 H300 Z;
        M300,420 Q350,405 400,420 T500,420 V450 H300 Z"/>
    <animateTransform attributeName="transform" type="translate"
      values="0 0;0 -180;0 -180" keyTimes="0;0.8;1" dur="6s" fill="freeze"/>
  </path>
</g>
```

波の`d`は3値とも同じコマンド構成（`M Q T V H Z`）にしてモーフィングの制約を満たしている。上昇量（`-180`）は容器の高さに合わせて調整する。

## 11. 放射状バースト — 中心から複数方向に広がる

用途：告知、発見の拡散、「一つの出来事が周囲に伝わる」瞬間。パターン6（分岐拡大）が経路の枝分かれなのに対し、こちらは方向を持たない拡散。

```svg
<circle cx="400" cy="300" r="10" fill="var(--yellow)">
  <animate attributeName="r" values="10;10;4" keyTimes="0;0.2;1" dur="2s" repeatCount="indefinite"/>
</circle>
<!-- 下のgを rotate(60 400 300), rotate(120 400 300)... と角度違いで5回複製し、6方向に配置する -->
<g transform="rotate(0 400 300)">
  <circle cx="400" cy="300" r="5" fill="var(--blue-mid)" opacity="0">
    <animateMotion path="M0,0 L0,-120" begin="0.2s" dur="1.4s" repeatCount="indefinite"/>
    <animate attributeName="opacity" values="0;1;0" begin="0.2s" dur="1.4s" repeatCount="indefinite"/>
  </circle>
</g>
```

## 12. ワイプ／リビール — 領域そのものを段階的に明かす

用途：隠されていた情報の開示、線ではなく面・テキストブロックの登場。dash-draw（線画描画）が線向けなのに対し、こちらは矩形・テキスト・画像などの面向け。

```svg
<clipPath id="reveal">
  <rect x="0" y="0" width="0" height="600">
    <animate attributeName="width" from="0" to="800" dur="1.6s" fill="freeze"/>
  </rect>
</clipPath>
<g clip-path="url(#reveal)">
  <rect x="0" y="0" width="800" height="600" fill="var(--panel-bg)"/>
  <text x="60" y="300" font-size="28" fill="var(--ink)">詳細がここに現れる</text>
</g>
```

## 13. グラデーションシマー — 光の帯が掃過して注目を作る

用途：数値・見出し・カードへの強調、「価値がある」ことのさりげない演出。

```svg
<defs>
  <linearGradient id="shimmer" x1="0" y1="0" x2="1" y2="0">
    <stop offset="0" stop-color="var(--gold)" stop-opacity="0"/>
    <stop offset="0.5" stop-color="var(--gold)" stop-opacity="0.9"/>
    <stop offset="1" stop-color="var(--gold)" stop-opacity="0"/>
    <animateTransform attributeName="gradientTransform" type="translate"
      values="-1 0;1 0" dur="2.4s" repeatCount="indefinite"/>
  </linearGradient>
</defs>
<rect x="200" y="240" width="400" height="80" rx="10" fill="var(--panel-bg)"/>
<rect x="200" y="240" width="400" height="80" rx="10" fill="url(#shimmer)"/>
```

`<animateTransform>` は `<stop>` ではなく `<linearGradient>` 要素直下に置く点に注意。

## 14. テキストオンパス追従ラベル — ラベルが経路を移動する

用途：ロードマップの「今ここ」、指標が推移するルートへの注釈。

```svg
<path id="routePath" d="M 80,500 Q 400,500 400,300 T 720,100" fill="none" stroke="var(--line)"/>
<text font-size="16" fill="var(--ink)">
  <textPath href="#routePath" startOffset="0%">
    <animate attributeName="startOffset" values="0%;100%" dur="4s" repeatCount="indefinite"/>
    現在地
  </textPath>
</text>
```

## 15. 比較バーレース — 伸びる速度の違いで優劣が入れ替わる

用途：施策A/Bの比較、後発が追い抜く様子、「差が縮まり逆転する」ドラマ。

```svg
<rect x="200" y="420" width="0" height="30" fill="var(--blue)">
  <animate attributeName="width" values="0;180;180" keyTimes="0;0.7;1" dur="3s" repeatCount="indefinite"/>
</rect>
<rect x="200" y="470" width="0" height="30" fill="var(--aqua)">
  <animate attributeName="width" values="0;140;260" keyTimes="0;0.7;1" dur="3s" repeatCount="indefinite"/>
</rect>
```

`keyTimes` の途中に交点（逆転する瞬間）が来るように2本の `values` を調整する。

## 16. ネットワーク・ノード点灯 — 情報が接続を辿って伝わる

用途：情報伝播、組織内の連鎖、「1箇所の気づきが全体に広がる」様子。パターン2（接続パルス）の多ノード版。

```svg
<g stroke="var(--line)" stroke-width="2" fill="none">
  <line x1="200" y1="300" x2="380" y2="200"/>
  <line x1="200" y1="300" x2="380" y2="400"/>
  <line x1="380" y1="200" x2="560" y2="300"/>
  <line x1="380" y1="400" x2="560" y2="300"/>
</g>
<circle cx="200" cy="300" r="10" fill="var(--ink-soft)">
  <animate attributeName="fill" values="var(--ink-soft);var(--yellow);var(--yellow)" keyTimes="0;0.1;1" dur="3s" repeatCount="indefinite"/>
</circle>
<circle cx="380" cy="200" r="10" fill="var(--ink-soft)">
  <animate attributeName="fill" values="var(--ink-soft);var(--ink-soft);var(--yellow);var(--yellow)" keyTimes="0;0.25;0.35;1" dur="3s" repeatCount="indefinite"/>
</circle>
<!-- 残りのノードも同様に、keyTimesの点灯タイミングをネットワーク上の距離に応じてずらす -->
```

## 17. viewBoxズーム — 全体像から詳細へ焦点を移す

用途：俯瞰から個別事例へ、マクロからミクロへの視点移動。

```svg
<svg viewBox="0 0 800 600">
  <animate attributeName="viewBox"
    values="0 0 800 600;0 0 800 600;260 180 280 210" keyTimes="0;0.3;1"
    calcMode="spline" keySplines="0.3 0 0.7 1;0.3 0 0.7 1"
    dur="3.5s" begin="1s" fill="freeze"/>
  <!-- 全体図の要素、ズーム先の座標(260,180)-(540,390)付近に詳細を描いておく -->
</svg>
```

`viewBox` は外側の `<svg>` 要素自身に対してアニメーションする。インラインSVGであれば問題なく動く。

## 18. モーフィング — 形そのものが変わる

用途：状態Aから状態Bへの本質的な変化（課題の形→解決策の形、旧ロゴ→新ロゴなど）。パターン3（データ→行動）が「配置」の変換なのに対し、こちらは「輪郭」の変換。

```svg
<path fill="var(--blue)" d="M 400,220 L 460,300 L 400,380 L 340,300 Z">
  <animate attributeName="d" dur="2.4s" repeatCount="indefinite"
    values="
      M 400,220 L 460,300 L 400,380 L 340,300 Z;
      M 400,220 C 460,220 460,380 400,380 C 340,380 340,220 400,220 Z;
      M 400,220 L 460,300 L 400,380 L 340,300 Z"/>
</path>
```

菱形（`L`×4）と円もどき（`C`×4）を同じ4セグメント構成で揃えているのがポイント。コマンド数・種類が変形前後で一致しない場合は [references/fundamentals.md](fundamentals.md) の「シェイプモーフィングの制約と対処」を参照。

## 19. 弾性バウンド — オーバーシュートして着地する

用途：決定が下る、結論に至る、着地して安定する締めの一手。

```svg
<circle cx="400" cy="120" r="14" fill="var(--blue)">
  <animate attributeName="cy" values="120;420;390;410;400" keyTimes="0;0.6;0.75;0.9;1"
    calcMode="spline"
    keySplines="0.3 0 0.7 1;0.3 0 0.7 1;0.3 0 0.7 1;0.3 0 0.7 1"
    dur="1.6s" fill="freeze"/>
</circle>
```

## 20. スクロール連動プログレッシブ・リビール（CSS、ページスクロール前提）

用途：ランディングページやレポートのように**ページ自体がスクロールする**構成で、読み進めるにつれて図が完成していく演出。`slide-deck-html` の固定ステージでは使えない（スクロールが発生しないため）。

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

詳細は [references/fundamentals.md](fundamentals.md) の「CSSスクロール連動アニメーション」を参照。

## reduced-motion対応（共通）

```css
@media (prefers-reduced-motion: reduce) {
  svg animate, svg animateTransform, svg animateMotion { animation-play-state: paused; }
}
```
```js
if (matchMedia('(prefers-reduced-motion: reduce)').matches) {
  document.querySelectorAll('svg').forEach(svg => svg.pauseAnimations && svg.pauseAnimations());
}
```

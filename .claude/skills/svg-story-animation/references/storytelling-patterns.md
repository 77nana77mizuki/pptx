# ストーリーテリング・パターン集

「作る前の3つの問い」（SKILL.md参照）で仕組みを特定したら、対応するパターンを開いてコピーし、座標や色を調整する。すべてネイティブSVGの `<animate>` / `<animateTransform>` / `<animateMotion>` のみで構成し、CSSキーフレームには頼らない。色は呼び出し元（スライド側など）で定義された変数（例：`var(--blue)`）を想定しているので、環境に合わせて置き換える。

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

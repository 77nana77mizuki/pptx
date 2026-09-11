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
| 統合・完成（部品が集まって1つの成果物になる） | 21（分解→組立） |
| 因果の連鎖（1つの出来事が次々に波及する） | 22（ドミノ連鎖） |
| 空間移動・拠点展開（俯瞰しながら経路を辿る） | 23（経路描画＋カメラパン） |
| 階層・意思決定の枝分かれ（組織図・決定木） | 24（樹形成長）／6（分岐拡大） |
| 稼働中・生存確認（施策が今も動いている実感） | 25（鼓動・波形） |
| 協働・連携が編み込まれる | 26（織り込み） |
| 断片が意味のある像を結ぶ（点や事実が全体像になる） | 27（結晶化） |
| 比較のBefore/After（同じ場面の2状態を切り替えて見せる） | 28（ワイプ比較）／15（比較バーレース） |
| 語順で意味が立ち上がる強調（キーワードだけ効かせる） | 29（キネティック・タイポグラフィ） |
| データが生まれる瞬間（数値そのものの発生を見せる） | 30（データ・グロース） |

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

## 21. 分解→組立（エクスプローデッドビュー）— 散らばった部品が1つの成果物になる

用途：複数の施策・要素が組み合わさって1つの成果になる、バラバラだった機能が統合される瞬間。パターン1（収束）が「点」に集まるのに対し、こちらは複数の部品がそれぞれ定位置に収まり「形」を完成させる。

```svg
<g>
  <rect x="120" y="80"  width="60" height="60" fill="var(--blue)">
    <animateTransform attributeName="transform" type="translate" values="0 0;280 220;280 220" keyTimes="0;0.7;1" dur="2.4s" calcMode="spline" keySplines="0.3 0 0.7 1;0.3 0 0.7 1" fill="freeze"/>
  </rect>
  <rect x="600" y="120" width="60" height="60" fill="var(--aqua)">
    <animateTransform attributeName="transform" type="translate" values="0 0;-220 180;-220 180" keyTimes="0;0.7;1" dur="2.4s" begin="0.15s" calcMode="spline" keySplines="0.3 0 0.7 1;0.3 0 0.7 1" fill="freeze"/>
  </rect>
  <!-- 3つ目以降のピースも同様に、終点座標が1つの完成形（例: 中央の正方形）を構成するよう配置する -->
</g>
```

各ピースの終点座標を先に決めて完成形を描き、そこから逆算して開始位置を散らすと破綻しにくい。3〜4ピースまでが目安（「作る前の3つの問い」の主役数上限と同じ理由）。

## 22. ドミノ連鎖 — 1つの出来事が次々に波及する

用途：最初の小さな気づき・決定が、関係者や工程へ順番に伝播していく様子。パターン16（ネットワーク点灯）が同時多発的な広がりなのに対し、こちらは1本の順番が明確な連鎖。

```svg
<g transform-origin="60 200">
  <rect id="d1" x="50" y="140" width="20" height="120" fill="var(--blue)">
    <animateTransform attributeName="transform" type="rotate" values="0 60 260;70 60 260" dur="0.35s" begin="0s" fill="freeze"/>
  </rect>
</g>
<g transform-origin="160 200">
  <rect id="d2" x="150" y="140" width="20" height="120" fill="var(--blue)">
    <animateTransform attributeName="transform" type="rotate" values="0 160 260;70 160 260" dur="0.35s" begin="d1.end" fill="freeze"/>
  </rect>
</g>
<!-- d3, d4... も begin="d2.end" のように前の要素の終了を起点に連鎖させる -->
```

`begin` を秒数の決め打ちではなく `前要素.end` で連鎖させるのが要点。後から駒を増減しても全体のタイミングが自動で追従する。

## 23. 経路描画＋カメラパン — 俯瞰しながら拠点・経路を辿る

用途：複数拠点の展開、視察・出張のような移動、地図やフロア図の上で視点そのものが移動する説明。パターン17（viewBoxズーム）が静止した1点への拡大なのに対し、こちらは経路に沿って視点が連続的に移動する。

```svg
<svg viewBox="0 0 1000 600">
  <path id="route" d="M 120,500 Q 400,520 480,320 T 860,140" fill="none" stroke="var(--line)" stroke-width="3"
        stroke-dasharray="900" stroke-dashoffset="900">
    <animate attributeName="stroke-dashoffset" from="900" to="0" dur="3.5s" fill="freeze"/>
  </path>
  <circle r="9" fill="var(--yellow)">
    <animateMotion dur="3.5s" fill="freeze" rotate="auto">
      <mpath href="#route"/>
    </animateMotion>
  </circle>
  <!-- viewBox自体を経路の進行に合わせて少しずつパンさせる（始点付近→終点付近） -->
  <animate attributeName="viewBox"
    values="0 200 1000 600;0 200 1000 600;300 0 1000 600" keyTimes="0;0.15;1"
    calcMode="spline" keySplines="0.3 0 0.7 1;0.3 0 0.7 1" dur="3.5s" fill="freeze"/>
</svg>
```

拠点マーカー（`circle`）は経路上の到達タイミングに合わせて `opacity` を `0→1` にすると「訪問した瞬間」を示せる。

## 24. 樹形成長 — 意思決定木・組織階層が枝ごとに育つ

用途：組織図、意思決定の分岐、段階的に詳細化していく階層構造。パターン6（分岐拡大）が同時展開なのに対し、こちらは幹→枝→葉の順に生えていく時間差を持たせる。

```svg
<path id="trunk" d="M 400,500 L 400,340" stroke="var(--line)" stroke-width="4" fill="none"
      stroke-dasharray="160" stroke-dashoffset="160">
  <animate attributeName="stroke-dashoffset" from="160" to="0" dur="0.8s" fill="freeze"/>
</path>
<path id="branchL" d="M 400,340 L 280,220" stroke="var(--line)" stroke-width="3" fill="none"
      stroke-dasharray="160" stroke-dashoffset="160">
  <animate attributeName="stroke-dashoffset" from="160" to="0" dur="0.6s" begin="trunk.end" fill="freeze"/>
</path>
<circle cx="280" cy="220" r="8" fill="var(--blue-mid)" opacity="0">
  <animate attributeName="opacity" from="0" to="1" dur="0.2s" begin="branchL.end" fill="freeze"/>
</circle>
<!-- branchR, さらに下位の枝も同様に前の枝の.endを起点に連鎖させる -->
```

深い階層でも「今どの枝を説明しているか」が時間軸として明確になる。項目数が多い場合は主要な2〜3段だけをアニメーションさせ、末端は静止画で一括表示してよい。

## 25. 鼓動・波形 — 稼働中・生存確認のメタファー

用途：施策や仕組みが「今も動き続けている」実感、モニタリング・ヘルスチェックの比喩。

```svg
<polyline points="40,200 160,200 190,120 220,280 250,160 280,200 400,200"
          fill="none" stroke="var(--aqua)" stroke-width="3" stroke-linecap="round" stroke-linejoin="round">
  <animateTransform attributeName="transform" type="translate"
    values="0 0;-360 0" dur="2.2s" repeatCount="indefinite" calcMode="linear"/>
</polyline>
<!-- 同じpolylineをx+360した複製をもう1つ重ね、同じtranslateで繋ぎ目なくループさせる -->
```

波形の複製をつなげてループさせる際は、2本の開始・終了のY座標を一致させてつなぎ目の段差が出ないようにする。ループが速すぎると不安を煽る見た目になるので、周期は2秒以上を目安にする。

## 26. 織り込み — 協働・連携が編み込まれる

用途：複数の主体（部門・パートナー・データ）が独立に動きながら1つの成果に織り込まれていく様子。パターン9（グーイー結合）が「融合して境界が消える」のに対し、こちらは互いの独立性を保ったまま「絡み合う」。

```svg
<path d="M 100,150 C 250,150 250,450 400,450 S 550,150 700,150" fill="none" stroke="var(--blue)" stroke-width="6" stroke-linecap="round"/>
<path d="M 100,450 C 250,450 250,150 400,150 S 550,450 700,450" fill="none" stroke="var(--aqua)" stroke-width="6" stroke-linecap="round" opacity="0.9"/>
<!-- 交差点で手前になる方を切り替えたい場合は、交差点付近だけ別要素に分割してz-orderを前後させる -->
```

2本の`S`（スムーズ曲線）を左右対称に描くのが基本形。本格的に上下が交互に入れ替わる「本物の編み込み」にしたい場合は交差点ごとに要素を分割する必要があるため、まずはこの簡略版（2本が交差するだけ）で十分な場面が多い。

## 27. 結晶化 — 断片が集まって意味のある像を結ぶ

用途：バラバラな事実・データ点が、集まることで初めて1つの全体像・気づきとして立ち上がる瞬間。パターン1（収束）が「1点」への収束なのに対し、こちらは複数点が集まって「輪郭のある形」を作る。

```svg
<g id="fragments">
  <circle r="4" fill="var(--ink-soft)"><animateMotion path="M 0,0 L 220,180" dur="1.8s" fill="freeze"/></circle>
  <circle r="4" fill="var(--ink-soft)"><animateMotion path="M 800,0 L 380,140" dur="1.8s" fill="freeze"/></circle>
  <circle r="4" fill="var(--ink-soft)"><animateMotion path="M 0,500 L 300,320" dur="1.8s" fill="freeze"/></circle>
  <!-- 各断片の終点が、下の輪郭線上の点に一致するよう配置する -->
</g>
<path d="M 220,180 L 380,140 L 460,260 L 300,320 Z" fill="none" stroke="var(--yellow)" stroke-width="3"
      stroke-dasharray="500" stroke-dashoffset="500" opacity="0">
  <animate attributeName="opacity" from="0" to="1" dur="0.1s" begin="1.7s" fill="freeze"/>
  <animate attributeName="stroke-dashoffset" from="500" to="0" dur="0.9s" begin="1.7s" fill="freeze"/>
</path>
```

断片の終点座標を輪郭パスの頂点に正確に合わせるのが要点。ずれていると「集まった」感が出ない。

## 28. ワイプ比較 — 同じ場面のBefore/Afterを1枚で切り替える

用途：施策の前後、旧UI/新UI、旧体制/新体制など「同じ構図のまま中身が変わった」ことを見せる。パターン12（ワイプ／リビール）が無地からの出現なのに対し、こちらは背後に「Before」の絵がすでにある状態から「After」で置き換える。

```svg
<g id="before"><!-- Before側のイラスト・図をここに --></g>
<clipPath id="afterClip">
  <rect x="0" y="0" width="0" height="600">
    <animate attributeName="width" from="0" to="800" dur="1.4s" begin="click" fill="freeze"/>
  </rect>
</clipPath>
<g clip-path="url(#afterClip)"><!-- After側のイラスト・図をここに（Beforeと同じ座標系で描く） --></g>
<line x1="0" y1="0" x2="0" y2="600" stroke="var(--accent)" stroke-width="3">
  <animate attributeName="x1" from="0" to="800" dur="1.4s" begin="click" fill="freeze"/>
  <animate attributeName="x2" from="0" to="800" dur="1.4s" begin="click" fill="freeze"/>
</line>
```

境界線（`line`）を動く仕切りとして重ねると「今どこまでがAfterか」が明確になる。自動再生よりも `begin="click"` でユーザー操作に委ねる方が、比較を自分のペースで確認できる。

## 29. キネティック・タイポグラフィ — 語順で意味が立ち上がる

用途：短いフレーズの中で1語だけを強調して結論を印象づけたいとき。文章そのものが主役になる、装飾を最小にしたページに向く。

```svg
<text x="80" y="200" font-size="56" fill="var(--ink-soft)">
  <tspan opacity="0"><animate attributeName="opacity" from="0" to="1" dur="0.4s" begin="0s" fill="freeze"/>選択肢が増えるほど、</tspan>
</text>
<text x="80" y="270" font-size="56" fill="var(--ink)">
  <tspan opacity="0"><animate attributeName="opacity" from="0" to="1" dur="0.4s" begin="0.5s" fill="freeze"/>進むべき道は</tspan>
  <tspan fill="var(--accent)" font-weight="700" opacity="0">
    <animate attributeName="opacity" from="0" to="1" dur="0.4s" begin="1s" fill="freeze"/>
    見えにくくなる。
  </tspan>
</text>
```

強調語だけ `fill` と `font-weight` を変え、他の語より僅かに遅れて登場させる。全語を同じ速度で流すと「ただのフェードイン」になり意味が立ち上がらないため、強調語の前に一拍（0.2〜0.3秒）の間を空けるのが要点。多用すると読みづらくなるので1ページに1回まで。

## 30. データ・グロース — 数値が発生する瞬間を見せる

用途：`data-graphics` の棒グラフ・面グラフに「今まさにこの数字が生まれた」という時間軸を足したいとき。静止画のグラフに動きを重ねる、2スキルの接点にあたるパターン。

```svg
<line x1="60" y1="400" x2="760" y2="400" stroke="currentColor" stroke-opacity=".4"/>
<rect x="100" y="400" width="80" height="0" fill="var(--neutral)">
  <animate attributeName="y" values="400;220" dur="1s" calcMode="spline" keySplines="0.3 0 0.7 1" begin="0s" fill="freeze"/>
  <animate attributeName="height" values="0;180" dur="1s" calcMode="spline" keySplines="0.3 0 0.7 1" begin="0s" fill="freeze"/>
</rect>
<rect x="300" y="400" width="80" height="0" fill="var(--accent)">
  <animate attributeName="y" values="400;140" dur="1s" calcMode="spline" keySplines="0.3 0 0.7 1" begin="0.15s" fill="freeze"/>
  <animate attributeName="height" values="0;260" dur="1s" calcMode="spline" keySplines="0.3 0 0.7 1" begin="0.15s" fill="freeze"/>
</rect>
```

`y` と `height` を同時に動かして「下端は固定のまま上に伸びる」形にするのが要点（`height` だけ動かすと上端が固定され下に伸びる誤った見た目になる）。棒ごとに `begin` を0.1〜0.2秒ずつずらすと「順番に確定していく」印象になる。`fill="freeze"` で最終状態を保持し、[data-graphics/references/charts.md](../../data-graphics/references/charts.md) の静止画テンプレートと数値・座標を揃える。

## 複合シーン — パターンを重ねて1つのストーリーにする

1シーンに1パターンだけで足りない場合は、**同じ「主役3〜4個以内」の制約を保ったまま**複数パターンを時間軸でつなぐ。例えば「27（結晶化）で断片が集まって輪郭ができる → 8（グロー）で結論の数字が光る」のように、`begin="前アニメーションのid.end"` で連結すると1本の物語になる。やってはいけないのは、複数の仕組み（収束と循環など）を**同時に**同じ画面で見せて主張を薄めること——時間差でつなぐのは良いが、同時多発は「作る前の3つの問い」の問1（1つの仕組みを説明しているか）に反する。

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

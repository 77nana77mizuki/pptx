# グラフの設計とテンプレート

出典: Financial Times `chart-doctor` の Visual Vocabulary（分類法）、Cleveland & McGill (1984) の知覚精度階層、Knaflic『Storytelling with Data』のデクラッター原則。

## チャート種類の選び方

FT Visual Vocabulary に沿って、「データの関係性」からチャート種類を決める。

| 見せたい関係性 | 選ぶチャート | 避けるもの |
|---|---|---|
| **比較**（項目間の大小） | 横棒・縦棒グラフ | 3D棒グラフ、装飾的なアイコン置き換え |
| **推移**（時系列） | 折れ線グラフ、面グラフ | ポイントが多すぎる散布図的な折れ線（5系列超は小分けにする） |
| **構成比**（部分と全体） | ドーナツ（4区分まで）、積み上げ棒 | 区分5個超の円グラフ、3Dドーナツ |
| **相関** | 散布図 | 軸を省略した相関の主張（回帰線なしの「相関がある」断定） |
| **分布** | ヒストグラム、箱ひげ図 | 母数が伝わらない単一の平均値だけの棒 |
| **偏差**（基準からの増減の積み上げ） | ウォーターフォール | 単純な棒グラフでの増減混在（符号が読み取りにくい） |
| **段階的な絞り込み**（歩留まり・離脱） | ファネル | 円グラフでの歩留まり表現（角度の比較は不正確） |
| **多軸の評価**（複数指標のバランス） | レーダー（スパイダー） | 5軸を超える多軸（頂点が多いほど形の比較が難しくなる） |
| **目標に対する実績**（単一KPIの達成度） | ブレット（bullet）チャート | ゲージ・スピードメーター（角度は知覚精度が低い） |
| **順位の入れ替わり**（複数期間のランキング変動） | バンプ／スロープチャート | 積み上げ棒での順位表現（読み取りにくい） |
| **数量の実感**（具体的な個数の大小） | ピクトグラム（アイソタイプ） | 巨大な単一の数字だけ（実感が伴わない） |
| **多変量の分布・相関**（行×列のマトリクス） | ヒートマップ | 色数の多い3D表 |

迷ったら、まず「比較」か「推移」のどちらかに単純化できないか検討する。複雑な複合グラフ（二軸グラフ、積み上げ＋折れ線の混在）は読み手の認知負荷が高く、`slide-deck-html` の「1スライド1メッセージ」にも反する。

## 知覚精度の高い順にエンコードする

Cleveland & McGill の実験に基づく精度順（上ほど正確に読み取れる）：

**位置 ＞ 長さ ＞ 角度 ＞ 面積 ＞ 色（の濃淡）**

棒グラフ（長さ・位置）が円グラフ（角度）やバブルチャート（面積）より正確に伝わるのはこの理由による。「かっこよく見えるから」でバブルや3D表現を選ばない。

## 共通ルール

- **凡例より直接ラベリング。** 系列の近くに直接、系列名と最終値を書く。凡例を探して視線が往復する構造を避ける。
- **軸線・グリッド線は最小限。** 0のベースライン1本があれば十分なことが多い。目盛りの補助線は必要な場合だけ薄いグレーで。
- **アクセントカラーは1系列・1本だけ。** 他はニュートラルなグレー系にし、伝えたい1本だけを強調する。
- **棒グラフの軸は0から始める。** 0から始めないと長さの比較が歪み、知覚精度の前提が崩れる。

## SVGテンプレート

いずれも `viewBox` はそのまま比率を保って `slide-deck-html` のコンテンツ領域に埋め込める。`--accent` / `--neutral` は実際のカラー値に読み替える。

### 縦棒グラフ（比較）

```html
<svg viewBox="0 0 800 480" role="img" aria-label="拠点別売上比較">
  <line x1="60" y1="400" x2="760" y2="400" stroke="currentColor" stroke-opacity=".4"/>
  <!-- 棒: x, 幅80, 高さは値に比例。強調したい1本だけ accent 色にする -->
  <rect x="100" y="220" width="80" height="180" fill="var(--neutral)"/>
  <text x="140" y="205" text-anchor="middle" font-size="22">128.4</text>
  <text x="140" y="430" text-anchor="middle" font-size="18">東日本</text>

  <rect x="300" y="140" width="80" height="260" fill="var(--accent)"/>
  <text x="340" y="125" text-anchor="middle" font-size="22" font-weight="700">94.7</text>
  <text x="340" y="430" text-anchor="middle" font-size="18">中部</text>

  <rect x="500" y="260" width="80" height="140" fill="var(--neutral)"/>
  <text x="540" y="245" text-anchor="middle" font-size="22">81.2</text>
  <text x="540" y="430" text-anchor="middle" font-size="18">西日本</text>
</svg>
```

### 折れ線グラフ（推移）— 直接ラベリング

```html
<svg viewBox="0 0 800 420" role="img" aria-label="4年間の推移">
  <line x1="60" y1="360" x2="760" y2="360" stroke="currentColor" stroke-opacity=".4"/>
  <polyline points="80,300 280,260 480,180 680,90"
            fill="none" stroke="var(--accent)" stroke-width="4" stroke-linecap="round"/>
  <circle cx="680" cy="90" r="6" fill="var(--accent)"/>
  <!-- 最終値だけ直接ラベル。凡例は置かない -->
  <text x="700" y="80" font-size="22" font-weight="700" fill="var(--accent)">128.4億円</text>
  <text x="80" y="385" font-size="16" text-anchor="middle">2022</text>
  <text x="280" y="385" font-size="16" text-anchor="middle">2023</text>
  <text x="480" y="385" font-size="16" text-anchor="middle">2024</text>
  <text x="680" y="385" font-size="16" text-anchor="middle">2025</text>
</svg>
```

複数系列を重ねる場合も凡例ではなく、各線の終端に系列名を直接添える。系列は最大4本まで（5本を超えたら小分け＝スモールマルチプルにする）。

### ドーナツ（構成比、4区分まで）

`stroke-dasharray` で円周の割合を切り出す。円周 = 2πr（r=70 なら約440）。

```html
<svg viewBox="0 0 300 300" role="img" aria-label="構成比">
  <circle cx="150" cy="150" r="70" fill="none" stroke="var(--neutral)" stroke-width="36"/>
  <!-- 強調したい区分（例: 62%）だけ accent 色にする。開始位置は12時から時計回り -->
  <circle cx="150" cy="150" r="70" fill="none" stroke="var(--accent)" stroke-width="36"
          stroke-dasharray="273 440" stroke-dashoffset="110" transform="rotate(-90 150 150)"/>
  <text x="150" y="142" text-anchor="middle" font-size="40" font-weight="700">62%</text>
  <text x="150" y="172" text-anchor="middle" font-size="16">主力事業</text>
</svg>
```

中心のテキストには「一番伝えたい1つの数字」だけを置く。区分ごとの内訳を全部読ませたいなら、ドーナツではなく表（[tables.md](tables.md)）を使う。

### ウォーターフォール（偏差・増減の積み上げ）

開始値から終了値までの間を、増減の内訳（各要因）として積み上げて見せる。各バーの下端は前のバーの終端に揃える。増加は accent、減少は別の中立トーン（グレーがかった赤系ではなく、ニュートラルなグレーで十分なことが多い）に分ける。

```html
<svg viewBox="0 0 800 420" role="img" aria-label="増減要因の内訳">
  <line x1="40" y1="360" x2="760" y2="360" stroke="currentColor" stroke-opacity=".3"/>
  <!-- 開始値 -->
  <rect x="60"  y="240" width="90" height="120" fill="var(--neutral)"/>
  <text x="105" y="225" text-anchor="middle" font-size="18">100</text>
  <text x="105" y="385" text-anchor="middle" font-size="15">期首</text>
  <!-- 増加要因: 下端を前バーの終端(240)に合わせて上に積む -->
  <rect x="200" y="160" width="90" height="80" fill="var(--accent)"/>
  <text x="245" y="145" text-anchor="middle" font-size="18" font-weight="700">+40</text>
  <text x="245" y="385" text-anchor="middle" font-size="15">新規獲得</text>
  <!-- 減少要因: 前バーの終端(160)から下に -->
  <rect x="340" y="160" width="90" height="50" fill="var(--neutral)" opacity=".6"/>
  <text x="385" y="145" text-anchor="middle" font-size="18">-20</text>
  <text x="385" y="385" text-anchor="middle" font-size="15">解約</text>
  <!-- 終了値: ゼロベースの通常の棒に戻す -->
  <rect x="480" y="210" width="90" height="150" fill="var(--ink)"/>
  <text x="525" y="195" text-anchor="middle" font-size="18" font-weight="700">120</text>
  <text x="525" y="385" text-anchor="middle" font-size="15">期末</text>
  <!-- バー間を薄い破線で結ぶと積み上げの連続性が伝わる -->
  <line x1="150" y1="240" x2="200" y2="240" stroke="currentColor" stroke-opacity=".25" stroke-dasharray="4 4"/>
  <line x1="290" y1="160" x2="340" y2="160" stroke="currentColor" stroke-opacity=".25" stroke-dasharray="4 4"/>
  <line x1="430" y1="210" x2="480" y2="210" stroke="currentColor" stroke-opacity=".25" stroke-dasharray="4 4"/>
</svg>
```

期首・期末は色を変えてニュートラル要因と区別する（両方とも0からの絶対値であることが視覚的にもわかるように）。

### ファネル（段階的な絞り込み）

各段の幅を通過数に比例させ、台形を積み重ねる。5段を超えると読みにくくなるため、主要な段階にまとめる。

```html
<svg viewBox="0 0 600 360" role="img" aria-label="流入から成約までの推移">
  <polygon points="60,20 540,20 460,100 140,100" fill="var(--neutral)"/>
  <text x="300" y="66" text-anchor="middle" font-size="20" font-weight="700">10,000</text>
  <text x="560" y="66" font-size="15">流入</text>

  <polygon points="140,110 460,110 380,190 220,190" fill="var(--neutral)" opacity=".75"/>
  <text x="300" y="156" text-anchor="middle" font-size="20" font-weight="700">2,400</text>
  <text x="480" y="156" font-size="15">商談</text>

  <polygon points="220,200 380,200 340,280 260,280" fill="var(--accent)"/>
  <text x="300" y="246" text-anchor="middle" font-size="20" font-weight="700">640</text>
  <text x="400" y="246" font-size="15" font-weight="700">成約</text>
</svg>
```

各段の脇に絶対数を直接書く（歩留まり率はSo whatとしてタイトルや脇に一言添える）。段の高さは均等でよく、面積を通過数に厳密比例させる必要はない（幅の比較で十分伝わる）。

### レーダー（多軸評価）

軸は3〜6本まで。軸線は薄く、値を結ぶ多角形の塗りは半透明にして背後の軸ラベルが読めるようにする。比較対象は2つまで（3つ以上重ねると多角形同士が判別しにくくなる）。

```html
<svg viewBox="0 0 400 400" role="img" aria-label="5指標の評価">
  <!-- 背景の同心多角形(目盛り)は最小限、20%/60%/100%の3本程度 -->
  <polygon points="200,60 314,140 271,272 129,272 86,140" fill="none" stroke="currentColor" stroke-opacity=".15"/>
  <polygon points="200,20 360,132 297,332 103,332 40,132" fill="none" stroke="currentColor" stroke-opacity=".15"/>
  <!-- 軸線 -->
  <g stroke="currentColor" stroke-opacity=".2">
    <line x1="200" y1="200" x2="200" y2="20"/><line x1="200" y1="200" x2="360" y2="132"/>
    <line x1="200" y1="200" x2="297" y2="332"/><line x1="200" y1="200" x2="103" y2="332"/>
    <line x1="200" y1="200" x2="40" y2="132"/>
  </g>
  <!-- 実データの多角形 -->
  <polygon points="200,50 330,140 260,300 150,290 90,150" fill="var(--accent)" fill-opacity=".25" stroke="var(--accent)" stroke-width="3"/>
  <text x="200" y="10" text-anchor="middle" font-size="15">品質</text>
  <text x="370" y="132" font-size="15">速度</text>
  <text x="297" y="352" text-anchor="middle" font-size="15">コスト</text>
  <text x="103" y="352" text-anchor="middle" font-size="15">柔軟性</text>
  <text x="15" y="132" font-size="15">サポート</text>
</svg>
```

### ブレット（bullet）チャート — 目標に対する実績

背景に定性的な範囲（不十分／可／良好のようなグレーの濃淡帯）を敷き、実績バーと目標の縦線を重ねる。ゲージより横幅を取らず、複数KPIを縦に並べやすい。

```html
<svg viewBox="0 0 600 80" role="img" aria-label="目標に対する達成度">
  <rect x="120" y="20" width="180" height="40" fill="var(--neutral)" opacity=".25"/>
  <rect x="300" y="20" width="180" height="40" fill="var(--neutral)" opacity=".45"/>
  <rect x="120" y="32" width="330" height="16" fill="var(--ink)"/>
  <line x1="420" y1="14" x2="420" y2="66" stroke="var(--accent)" stroke-width="4"/>
  <text x="0" y="46" font-size="18">売上目標</text>
  <text x="460" y="46" font-size="16" fill="var(--accent)" font-weight="700">目標: 420</text>
</svg>
```

### バンプ／スロープチャート — 順位・数値の入れ替わり

2〜4時点間で、項目ごとの値（または順位）を線でつなぐ。線が交差する箇所が「逆転」であり、そこが伝えたい山場になることが多い。

```html
<svg viewBox="0 0 500 300" role="img" aria-label="施策A/Bの前後比較">
  <line x1="80" y1="60" x2="80" y2="260" stroke="currentColor" stroke-opacity=".2"/>
  <line x1="420" y1="60" x2="420" y2="260" stroke="currentColor" stroke-opacity=".2"/>
  <line x1="80" y1="220" x2="420" y2="100" stroke="var(--accent)" stroke-width="4"/>
  <line x1="80" y1="100" x2="420" y2="200" stroke="var(--neutral)" stroke-width="4"/>
  <text x="60" y="225" text-anchor="end" font-size="16">施策B</text>
  <text x="440" y="105" font-size="16" font-weight="700" fill="var(--accent)">施策B: 逆転</text>
  <text x="60" y="105" text-anchor="end" font-size="16">施策A</text>
  <text x="440" y="205" font-size="16">施策A</text>
</svg>
```

### ピクトグラム（アイソタイプ、数量の実感）

同じ記号を個数分だけ並べ、端数は塗りを部分的にする（`clipPath`で欠けを表現）。1記号が表す単位（例：1つ＝100件）を必ず明記する。50個を超える場合は1記号あたりの単位を大きくして数を減らす。

```html
<svg viewBox="0 0 500 120" role="img" aria-label="達成件数(1アイコン=100件)">
  <g fill="var(--accent)">
    <circle cx="30" cy="40" r="14"/><circle cx="70" cy="40" r="14"/><circle cx="110" cy="40" r="14"/>
    <circle cx="150" cy="40" r="14"/>
  </g>
  <!-- 端数(0.4個分)はclipPathで左40%だけ塗る -->
  <clipPath id="partial"><rect x="176" y="26" width="11.2" height="28"/></clipPath>
  <circle cx="190" cy="40" r="14" fill="var(--neutral)" opacity=".3"/>
  <circle cx="190" cy="40" r="14" fill="var(--accent)" clip-path="url(#partial)"/>
  <text x="0" y="90" font-size="15">1アイコン = 100件（合計 440件）</text>
</svg>
```

### ヒートマップ（行×列のマトリクス）

セルの濃淡だけで判断させず、必要ならセル内に数値も併記する。色相を変えず、1色の明度・彩度だけで濃淡を作ると誤読が少ない（知覚精度階層で色は最下位のため、正確な比較が要る場合は数値併記を優先する）。

```html
<svg viewBox="0 0 420 220" role="img" aria-label="拠点×月次の指標マトリクス">
  <g font-size="14">
    <rect x="100" y="20"  width="80" height="40" fill="var(--accent)" opacity=".3"/>
    <rect x="180" y="20"  width="80" height="40" fill="var(--accent)" opacity=".6"/>
    <rect x="260" y="20"  width="80" height="40" fill="var(--accent)" opacity=".9"/>
    <text x="140" y="45" text-anchor="middle">72</text>
    <text x="220" y="45" text-anchor="middle">85</text>
    <text x="300" y="45" text-anchor="middle" fill="#fff" font-weight="700">96</text>
    <text x="20" y="45">東日本</text>
  </g>
</svg>
```

## 統計ヒーロー（巨大数字を主役にする）

「この1つの数字が結論そのもの」というページでは、グラフではなく数字自体を大きく置くほうが強い。詳細は `slide-deck-html` の [layout-gallery.md](../../slide-deck-html/references/layout-gallery.md)「統計ヒーロー」と、`svg-story-animation` の [illustration-styles.md](../../svg-story-animation/references/illustration-styles.md)「タイポグラフィをビジュアルにする」を参照。数値の桁は `font-variant-numeric: tabular-nums` を当て、単位・注記はその1/6〜1/8程度のサイズに抑える。

## KPIスコアカード（複数指標を並列に見せる）

3〜4個までの主要指標を、数値＋前期比の矢印だけのカードで横並びにする。カードにドロップシャドウ・太い枠線を付けず、指標間はセーフエリア内の余白（`design-system.md`のスペーシングスケール）だけで区切る。

```html
<div class="kpi-row">
  <div class="kpi"><div class="kpi-value">128.4<span class="kpi-unit">億円</span></div><div class="kpi-delta up">+6.2%</div><div class="kpi-label">売上</div></div>
  <div class="kpi"><div class="kpi-value">94.7<span class="kpi-unit">億円</span></div><div class="kpi-delta up">+18.9%</div><div class="kpi-label">中部売上</div></div>
  <div class="kpi"><div class="kpi-value">4.2<span class="kpi-unit">%</span></div><div class="kpi-delta down">-0.8pt</div><div class="kpi-label">解約率</div></div>
</div>
```

```css
.kpi-row { display:flex; gap:64px; }
.kpi-value { font-size:56px; font-weight:800; font-variant-numeric:tabular-nums; }
.kpi-unit { font-size:22px; font-weight:400; margin-left:4px; }
.kpi-delta { font-size:18px; font-weight:700; }
.kpi-delta.up { color:var(--accent); } .kpi-delta.down { color:var(--ink-soft); }
.kpi-label { font-size:16px; color:var(--ink-soft); margin-top:6px; }
```

矢印記号（▲/▼）は装飾で足さず、色と符号（+/-）だけで十分なことが多い（信号機的な赤緑の多用は視覚的なAI臭になりやすい——`slide-deck-html`の[visual-slop-lexicon.md](../../slide-deck-html/references/visual-slop-lexicon.md)参照）。

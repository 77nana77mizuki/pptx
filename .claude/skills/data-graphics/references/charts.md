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

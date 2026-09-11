# タイムライン／ロードマップ／ガントの設計とテンプレート

出典: `cathrynlavery/diagram-design`（MIT License）のタイムライン・ガント図の構造（要素は装飾のためだけに置かない、アクセントカラーは1〜2箇所）を、本プロジェクトの1600×900ステージ向けに再構成。

## 種類の使い分け

| 種類 | 見せるもの | 向いている場面 |
|---|---|---|
| **横型タイムライン** | 単一の時系列に沿った出来事（マイルストーン） | 沿革、これまでの経緯、単線の年表 |
| **フェーズ帯** | 期間で区切られた段階とその特徴 | 計画のフェーズ分け、Before/After比較 |
| **ガント** | 複数のタスク・工程の並行関係と期間 | 詳細な工程表、担当と期間の対応 |

`svg-story-animation` との境界: ここで作るのは完成した静止画のレイアウト。「タイムラインが左から右へ順に描かれていく」ような演出を足したい場合は、`svg-story-animation` の「段階的進行」パターンをこの上に重ねる。

## 共通ルール

- **主線は1本、要素は主線の上に揃えて置く。** 浮かせたラベルや帯を乱立させない（`slide-deck-html` 規約と同じ理由）。
- **アクセントカラーは「今」「重要な1点」だけ。** 過去/完了フェーズはニュートラルなトーン、注目させたい現在地点や成果だけをアクセントにする。
- **日付・期間は主線の近くに直接置く。** 別枠の凡例に日付一覧を作らない。
- **項目数は5〜7個まで。** それ以上ある場合はフェーズでグルーピングするか、複数の図に分ける。

## SVGテンプレート

### 横型タイムライン（マイルストーン、上下交互配置）

```html
<svg viewBox="0 0 1200 300" role="img" aria-label="沿革">
  <line x1="60" y1="150" x2="1140" y2="150" stroke="currentColor" stroke-opacity=".35" stroke-width="2"/>

  <!-- マイルストーン: ラベルを上下交互に置いて重なりを防ぐ -->
  <circle cx="180" cy="150" r="8" fill="var(--neutral)"/>
  <text x="180" y="120" text-anchor="middle" font-size="16">2019</text>
  <text x="180" y="185" text-anchor="middle" font-size="15">事業開始</text>

  <circle cx="500" cy="150" r="8" fill="var(--neutral)"/>
  <text x="500" y="185" text-anchor="middle" font-size="16">2021</text>
  <text x="500" y="120" text-anchor="middle" font-size="15">海外展開</text>

  <!-- 現在地点だけアクセント色で強調 -->
  <circle cx="900" cy="150" r="11" fill="var(--accent)"/>
  <text x="900" y="120" text-anchor="middle" font-size="18" font-weight="700" fill="var(--accent)">2025</text>
  <text x="900" y="185" text-anchor="middle" font-size="16" font-weight="700">売上倍増</text>
</svg>
```

### フェーズ帯（Before/After・計画フェーズ）

```html
<svg viewBox="0 0 1200 260" role="img" aria-label="3フェーズの計画">
  <!-- 帯は隙間なく連結。幅は期間の長さに比例させる -->
  <rect x="0"   y="80" width="360" height="80" fill="var(--neutral)" opacity=".5"/>
  <rect x="360" y="80" width="480" height="80" fill="var(--neutral)" opacity=".8"/>
  <rect x="840" y="80" width="360" height="80" fill="var(--accent)"/>

  <text x="180" y="125" text-anchor="middle" font-size="18">Phase 1</text>
  <text x="600" y="125" text-anchor="middle" font-size="18">Phase 2</text>
  <text x="1020" y="125" text-anchor="middle" font-size="18" font-weight="700" fill="#fff">Phase 3</text>

  <text x="180" y="60" text-anchor="middle" font-size="15">2024 Q1–Q2</text>
  <text x="600" y="60" text-anchor="middle" font-size="15">2024 Q3–2025 Q1</text>
  <text x="1020" y="60" text-anchor="middle" font-size="15" font-weight="700">2025 Q2–Q3</text>
</svg>
```

### ガント（タスク × 期間）

行の高さ・間隔は4の倍数に揃えると縦の整列が崩れにくい。

```html
<svg viewBox="0 0 1200 260" role="img" aria-label="工程表">
  <!-- 週次などの目盛り線は必要な場合だけ薄く -->
  <line x1="260" y1="20" x2="260" y2="240" stroke="currentColor" stroke-opacity=".15"/>
  <line x1="500" y1="20" x2="500" y2="240" stroke="currentColor" stroke-opacity=".15"/>
  <line x1="740" y1="20" x2="740" y2="240" stroke="currentColor" stroke-opacity=".15"/>
  <line x1="980" y1="20" x2="980" y2="240" stroke="currentColor" stroke-opacity=".15"/>

  <text x="0" y="48" font-size="16">要件定義</text>
  <rect x="80" y="32" width="180" height="28" rx="4" fill="var(--neutral)"/>

  <text x="0" y="108" font-size="16">設計</text>
  <rect x="220" y="92" width="240" height="28" rx="4" fill="var(--neutral)"/>

  <text x="0" y="168" font-size="16">実装</text>
  <rect x="420" y="152" width="360" height="28" rx="4" fill="var(--accent)"/>

  <text x="0" y="228" font-size="16">リリース</text>
  <rect x="960" y="212" width="120" height="28" rx="4" fill="var(--neutral)"/>
</svg>
```

タスクが依存関係を持つ場合のみ、バーの端をつなぐ矢印（`marker-end`）を足す。依存が単純な直列なら矢印なしで十分読み取れることが多く、余計な線を足す前に本当に要るか確認する。

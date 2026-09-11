# ダイアグラム（関係性・構造を見せる図）

[charts.md](charts.md) が数値の大小・推移・構成比を見せる「チャート」を扱うのに対し、ここで扱うのは数値を持たない、または数値以上に**構造・関係性・位置づけ**そのものが主張になる図。コンサルティング資料や戦略資料で多用される型を、本プロジェクトの1600×900ステージ向けにSVGテンプレート化した。

## 種類の使い分け

| 見せたい構造 | 選ぶ図 | 向いている場面 |
|---|---|---|
| 2軸での位置づけ・優先順位 | クアドラント（2x2マトリクス） | 施策の優先度づけ、競合ポジショニング |
| 集合の重なり・共通部分 | ベン図 | 複数条件を満たす対象、共通課題の抽出 |
| 上下関係・指揮系統・分類階層 | 組織図／階層ツリー | 組織構造、カテゴリ分類、意思決定の分岐 |
| 入力から出力までの処理の流れ | フロー図（フローチャート） | 業務プロセス、承認フロー、判断分岐のある手順 |
| 複数の流れが合流・分岐する量の推移 | 簡易サンキー（フロー配分） | 予算配分、ユーザーの遷移先の内訳 |
| 2つの案・状態の対比評価 | 比較スケール／天秤 | トレードオフの提示、メリデメの重み比較 |

## クアドラント（2x2マトリクス）

2つの軸（例：緊急度×重要度、コスト×効果）でアイテムを配置する。象限ラベルは軸の外側ではなく、各象限の中に薄く直接置く。プロットする点は4〜8個程度まで（増えすぎたら象限ごとに代表点だけ残す）。

```html
<svg viewBox="0 0 600 600" role="img" aria-label="重要度と緊急度によるマトリクス">
  <line x1="60" y1="300" x2="560" y2="300" stroke="currentColor" stroke-opacity=".3"/>
  <line x1="300" y1="40" x2="300" y2="560" stroke="currentColor" stroke-opacity=".3"/>
  <text x="300" y="30" text-anchor="middle" font-size="16" fill="currentColor" opacity=".6">重要度: 高</text>
  <text x="300" y="585" text-anchor="middle" font-size="16" fill="currentColor" opacity=".6">重要度: 低</text>
  <text x="45" y="300" text-anchor="end" font-size="16" fill="currentColor" opacity=".6">緊急度低</text>
  <text x="565" y="300" font-size="16" fill="currentColor" opacity=".6">緊急度高</text>
  <!-- 象限ラベルは中に薄く -->
  <text x="430" y="150" text-anchor="middle" font-size="20" fill="var(--accent)" opacity=".5">最優先</text>
  <!-- プロット: 強調したい1点だけaccent、他はneutral -->
  <circle cx="440" cy="160" r="12" fill="var(--accent)"/>
  <text x="440" y="140" text-anchor="middle" font-size="15" font-weight="700">施策A</text>
  <circle cx="180" cy="200" r="10" fill="var(--neutral)"/>
  <text x="180" y="180" text-anchor="middle" font-size="15">施策B</text>
  <circle cx="400" cy="420" r="10" fill="var(--neutral)"/>
  <text x="400" y="400" text-anchor="middle" font-size="15">施策C</text>
</svg>
```

軸ラベルは「高／低」のような相対表現でよいが、可能なら具体的な基準（「3ヶ月以内」「売上1億円以上」等）を併記すると説得力が増す。

## ベン図（集合の重なり）

円は2つまでが基本。3つ以上は交差部分が小さくなり読みにくいため、本当に3集合の関係を見せる必要がある場合のみ使う。重なり部分のラベルは円の外に出さず、重なりの中心に直接置く。

```html
<svg viewBox="0 0 500 320" role="img" aria-label="2条件の重なり">
  <circle cx="200" cy="160" r="130" fill="var(--blue)" opacity=".55"/>
  <circle cx="320" cy="160" r="130" fill="var(--accent)" opacity=".55"/>
  <text x="140" y="165" text-anchor="middle" font-size="18" font-weight="700" fill="#fff">条件A</text>
  <text x="380" y="165" text-anchor="middle" font-size="18" font-weight="700" fill="#fff">条件B</text>
  <text x="260" y="165" text-anchor="middle" font-size="18" font-weight="700" fill="#fff">両方満たす</text>
</svg>
```

## 組織図／階層ツリー

主線（縦の幹）から左右に枝分かれさせる。ノードの箱に角丸・影を多用せず、線と塗りの円/矩形で十分な情報量にする。5階層を超える場合はページを分けるか、主要な2階層だけをこの図で見せ、詳細は表（[tables.md](tables.md)）に逃がす。

```html
<svg viewBox="0 0 800 300" role="img" aria-label="組織階層">
  <line x1="400" y1="40" x2="400" y2="90" stroke="currentColor" stroke-opacity=".3"/>
  <rect x="330" y="10" width="140" height="40" rx="4" fill="var(--ink)"/>
  <text x="400" y="35" text-anchor="middle" font-size="16" fill="#fff">経営企画部</text>

  <line x1="200" y1="90" x2="600" y2="90" stroke="currentColor" stroke-opacity=".3"/>
  <line x1="200" y1="90" x2="200" y2="130" stroke="currentColor" stroke-opacity=".3"/>
  <line x1="400" y1="90" x2="400" y2="130" stroke="currentColor" stroke-opacity=".3"/>
  <line x1="600" y1="90" x2="600" y2="130" stroke="currentColor" stroke-opacity=".3"/>

  <rect x="130" y="130" width="140" height="40" rx="4" fill="var(--neutral)"/>
  <text x="200" y="155" text-anchor="middle" font-size="15">戦略チーム</text>
  <rect x="330" y="130" width="140" height="40" rx="4" fill="var(--accent)"/>
  <text x="400" y="155" text-anchor="middle" font-size="15" font-weight="700" fill="#fff">DXチーム</text>
  <rect x="530" y="130" width="140" height="40" rx="4" fill="var(--neutral)"/>
  <text x="600" y="155" text-anchor="middle" font-size="15">広報チーム</text>
</svg>
```

`svg-story-animation`のパターン24（樹形成長）を重ねると、幹→枝の順に生えていく説明にできる。

## フロー図（フローチャート）

処理は角丸矩形、判断分岐は菱形、という最小限の記号だけで足りることが多い。矢印は`marker-end`で統一し、矢印の色を変えて意味を持たせない（分岐の意味はラベルの文字で持たせる）。

```html
<svg viewBox="0 0 700 220" role="img" aria-label="申請から承認までのフロー">
  <defs>
    <marker id="arrow" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="7" markerHeight="7" orient="auto">
      <path d="M0,0 L10,5 L0,10 Z" fill="currentColor" opacity=".6"/>
    </marker>
  </defs>
  <rect x="20" y="80" width="140" height="50" rx="8" fill="var(--neutral)"/>
  <text x="90" y="110" text-anchor="middle" font-size="15">申請</text>
  <line x1="160" y1="105" x2="230" y2="105" stroke="currentColor" stroke-opacity=".6" marker-end="url(#arrow)"/>

  <polygon points="300,60 380,105 300,150 220,105" fill="var(--neutral)" opacity=".7"/>
  <text x="300" y="110" text-anchor="middle" font-size="14">承認?</text>
  <line x1="380" y1="105" x2="450" y2="105" stroke="currentColor" stroke-opacity=".6" marker-end="url(#arrow)"/>
  <text x="415" y="95" text-anchor="middle" font-size="13">Yes</text>

  <rect x="450" y="80" width="140" height="50" rx="8" fill="var(--accent)"/>
  <text x="520" y="110" text-anchor="middle" font-size="15" font-weight="700" fill="#fff">実行</text>

  <line x1="300" y1="150" x2="300" y2="190" stroke="currentColor" stroke-opacity=".6" marker-end="url(#arrow)"/>
  <text x="315" y="175" font-size="13">No</text>
  <rect x="230" y="190" width="140" height="1" fill="none"/>
  <text x="300" y="210" text-anchor="middle" font-size="15">差し戻し</text>
</svg>
```

## 簡易サンキー（フロー配分）

帯の太さを量に比例させ、合流・分岐する箇所は滑らかな曲線（`C`コマンド）でつなぐ。3〜4本の流入・流出までが視認性の限界。

```html
<svg viewBox="0 0 700 300" role="img" aria-label="予算の配分">
  <path d="M 40,60 C 200,60 200,90 360,90 L 360,140 C 200,140 200,110 40,110 Z" fill="var(--blue)" opacity=".7"/>
  <path d="M 40,130 C 200,130 200,200 360,200 L 360,260 C 200,260 200,190 40,190 Z" fill="var(--accent)" opacity=".7"/>
  <text x="20" y="90" text-anchor="end" font-size="15">売上</text>
  <text x="380" y="115" font-size="15">開発費 60%</text>
  <text x="380" y="230" font-size="15" font-weight="700" fill="var(--accent)">販促費 40%</text>
</svg>
```

帯の始点・終点の太さ（この例では`60`と`50`のような差）を実際の比率に合わせて調整する。3系統を超える場合は表に切り替える判断も検討する（[tables.md](tables.md)「行数が多すぎるときは表を疑う」の逆で、系統が多すぎる場合も表の方が正確に伝わることがある）。

## 比較スケール／天秤（トレードオフの提示）

2つの選択肢を左右の皿に載せ、それぞれの重み（メリット・デメリットの数や強さ）で傾きを変える。傾きは大げさにせず10〜15度程度に留める。

```html
<svg viewBox="0 0 600 300" role="img" aria-label="2案の比較">
  <line x1="300" y1="60" x2="300" y2="220" stroke="currentColor" stroke-opacity=".4" stroke-width="4"/>
  <g transform="rotate(-8 300 90)">
    <line x1="140" y1="90" x2="460" y2="90" stroke="currentColor" stroke-opacity=".4" stroke-width="3"/>
    <rect x="100" y="90" width="100" height="14" rx="7" fill="var(--accent)"/>
    <rect x="420" y="98" width="100" height="14" rx="7" fill="var(--neutral)"/>
    <text x="150" y="80" text-anchor="middle" font-size="15" font-weight="700" fill="var(--accent)">案A</text>
    <text x="470" y="88" text-anchor="middle" font-size="15">案B</text>
  </g>
</svg>
```

「なぜ傾いているか」の根拠（評価軸・重みの内訳）を必ず脇に文字で添える。図だけで優劣を断定しない（`slide-deck-html`の「裏づけのない評価語」チェックと同じ理由）。

## 仕上げ

これらの図に動きで「配分が変わっていく」「階層が育っていく」といった説明を足したい場合は`svg-story-animation`の該当パターン（樹形成長・データ・グロース等）を重ねる。仕上げ前チェックは[checklist.md](checklist.md)に準じる。

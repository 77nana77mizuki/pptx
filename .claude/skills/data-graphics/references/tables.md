# 表の設計

出典: Cole Nussbaumer Knaflic『Storytelling with Data』の表デクラッター原則を、本プロジェクトの単一HTMLスライド向けに翻案。

## 原則

1. **縁取り・網掛け・太い罫線を削る。** 罫線は「見出しの下」など区切りが本当に要る場所だけに残し、それ以外は余白（padding）で区切る。全セルに罫線を引かない。
2. **数値は右揃え、文字は左揃え。** 桁の位取りが揃うと大小比較がしやすくなる。見出しは対応する列の揃えに合わせる。
3. **小数点の桁数をそろえる。** 列内で `12.3` と `12` が混在しない。単位（円／%／件）は見出しに1回だけ書き、セル内で繰り返さない。
4. **伝えたい1行・1列だけ強調する。** 背景色1色または太字で、結論に関係する行・列だけを目立たせる。強調が2箇所以上あると「結局どこを見ればいいか」が伝わらなくなる。
5. **意味のある順に並べる。** 五十音順・時系列順など機械的な並びが結論と関係ないなら、結論を支える指標（多い順など）でソートする。
6. **行数が多すぎるときは表を疑う。** 二桁後半以上の行がある、あるいは「傾向」を見せたいなら、表ではなくグラフにする（表は個々の値の参照に向き、傾向の"形"を見せるのには向かない）。
7. **ゼブラ縞は行数が多いときの最終手段。** 10行に満たないなら、罫線もゼブラも無しで足りることが多い。

## HTML/CSSテンプレート

`slide-deck-html` の1600×900ステージ内、コンテンツ幅に収まるサイズを想定。`--accent` はデッキ側で定義済みのアクセントカラー変数に読み替える。

```html
<table class="data-table">
  <thead>
    <tr>
      <th class="col-label">拠点</th>
      <th class="col-num">売上（億円）</th>
      <th class="col-num">前年比</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td class="col-label">東日本</td>
      <td class="col-num">128.4</td>
      <td class="col-num">+6.2%</td>
    </tr>
    <tr class="row-highlight">
      <td class="col-label">中部</td>
      <td class="col-num">94.7</td>
      <td class="col-num">+18.9%</td>
    </tr>
    <tr>
      <td class="col-label">西日本</td>
      <td class="col-num">81.2</td>
      <td class="col-num">+3.1%</td>
    </tr>
  </tbody>
</table>
```

```css
.data-table { width: 100%; border-collapse: collapse; font-variant-numeric: tabular-nums; }
.data-table th, .data-table td { padding: 14px 20px; border: none; }
.data-table thead th { text-align: left; border-bottom: 2px solid currentColor; opacity: .7; font-weight: 500; }
.data-table tbody tr + tr td { border-top: 1px solid rgba(128,128,128,.25); }
.data-table .col-num { text-align: right; }
.data-table .row-highlight { background: color-mix(in srgb, var(--accent) 12%, transparent); font-weight: 700; }
```

`row-highlight` は結論を支える1行だけに付ける。強調したい列（例：前年比が最大の行）を強調するときは、行ではなく該当セルにだけ `row-highlight` 相当のスタイルを当ててもよい。

## こういうときは表を使わない

- 3点以上の推移を見せたい → 折れ線・面グラフ（[charts.md](charts.md)）
- 全体に占める割合を見せたい／項目が5個以内 → ドーナツか棒グラフ
- 期間・工程の前後関係を見せたい → タイムライン系（[timelines.md](timelines.md)）

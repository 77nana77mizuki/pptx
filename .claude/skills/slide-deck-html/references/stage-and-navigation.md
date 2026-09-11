# 固定ステージとナビゲーションの実装

出典: `zarazhangrui/frontend-slides`（MIT License）の固定16:9ステージ設計を、本プロジェクトの1600×900サイズに翻案したもの。`vendored/viewport-base.css` に元の実装（1920×1080版）を同梱しているので、細部を見たいときはそちらも参照する。

## 設計原則

- スライドは1600×900の固定ピクセルサイズで作成する。画面全体には**1つのJS transform**（`translate + scale`）で拡大縮小し、レスポンシブブレイクポイントで中身を組み替えない（はみ出す場合はレターボックス/ピラーボックスで余白にする）。
- スライドの表示・非表示は `opacity` / `visibility` / `pointer-events` の3点で切り替える。`display` の切り替えは、後から追加したレイアウト用CSS（`.slide-content{display:flex}` 等）に上書きされて全スライドが同時に表示される事故が起きるため使わない。
- ナビゲーション（クリック・矢印キー・前後ボタン）と進捗バー・ページ番号は、固定ステージの**外側**（実ビューポート基準の `position:fixed`）に配置する。

## HTML構造

```html
<div class="deck-viewport">
  <div class="stage" id="stage">
    <section class="slide active" data-idx="0">...</section>
    <section class="slide" data-idx="1">...</section>
  </div>
</div>
<div class="deck-chrome">
  <button id="prevBtn" aria-label="前のスライド">←</button>
  <div class="progress"><div class="progress-fill" id="progressFill"></div></div>
  <span id="pageNum">1 / 11</span>
  <button id="nextBtn" aria-label="次のスライド">→</button>
</div>
```

## CSS（要点。全体は `vendored/viewport-base.css` を参照し1600×900に読み替える）

```css
html, body { width:100%; height:100%; margin:0; overflow:hidden; }
.deck-viewport { position:fixed; inset:0; overflow:hidden; }
.stage {
  position:absolute; left:0; top:0;
  width:1600px; height:900px;
  transform-origin:0 0; overflow:hidden;
}
.slide {
  position:absolute; inset:0; width:1600px; height:900px; overflow:hidden;
  visibility:hidden; opacity:0; pointer-events:none;
  transition:opacity .5s ease;
}
.slide.active { visibility:visible; opacity:1; pointer-events:auto; z-index:1; }

.deck-chrome {
  position:fixed; left:50%; bottom:26px; transform:translateX(-50%);
  display:flex; align-items:center; gap:18px; z-index:1000;
}
```

## JavaScript（ナビゲーションコントローラー）

```js
(function () {
  var slides = Array.prototype.slice.call(document.querySelectorAll('.slide'));
  var i = 0;
  var stage = document.getElementById('stage');
  var progressFill = document.getElementById('progressFill');
  var pageNum = document.getElementById('pageNum');

  function show(n) {
    i = Math.max(0, Math.min(n, slides.length - 1));
    slides.forEach(function (s, idx) { s.classList.toggle('active', idx === i); });
    progressFill.style.width = (i / (slides.length - 1) * 100) + '%';
    pageNum.textContent = (i + 1) + ' / ' + slides.length;
  }

  function scaleStage() {
    var s = Math.min(window.innerWidth / 1600, window.innerHeight / 900);
    var x = (window.innerWidth - 1600 * s) / 2;
    var y = (window.innerHeight - 900 * s) / 2;
    stage.style.transform = 'translate(' + x + 'px,' + y + 'px) scale(' + s + ')';
  }

  window.addEventListener('resize', scaleStage);
  scaleStage();
  show(0);

  document.getElementById('prevBtn').addEventListener('click', function (e) { e.stopPropagation(); show(i - 1); });
  document.getElementById('nextBtn').addEventListener('click', function (e) { e.stopPropagation(); show(i + 1); });
  document.addEventListener('keydown', function (e) {
    if (e.key === 'ArrowRight' || e.key === ' ') show(i + 1);
    if (e.key === 'ArrowLeft') show(i - 1);
  });
  stage.addEventListener('click', function () { show(i + 1); });
})();
```

## レンダリング検査

出力後は、可能であればヘッドレスブラウザ（Playwright等）で1600×900ビューポートを開き、全スライドをスクリーンショットして目視確認する。特に以下を見る。

- テキストがステージ右端・下端からはみ出してクリップされていないか（`overflow:hidden` は黙って切るので、はみ出しはスクリーンショットでしか気づけないことが多い）
- HTML側のオーバーレイ要素（タイトル・出典クレジット等）と、SVG側の装飾テキストが重なっていないか
- ページ番号・進捗バーが実際のスライド数と一致しているか

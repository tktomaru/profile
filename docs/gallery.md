---
layout: default
title: Gallery
description: とまが撮影したお気に入り写真ギャラリーです。
og_image: /images/ogp_gallery.png
og_image_width: 1200
og_image_height: 600
permalink: /gallery.html
---

# Gallery

ここは、とまが撮影した、お気に入り写真を載せています。

<hr class="hr3">
<hr class="hr2">

<div class="gc-row" id="gcSlideRow">
  <label class="gc-label" for="gcSlideInterval">スライド</label>

  <button id="gcSlideToggle" class="gc-btn" type="button" aria-pressed="false">
    開始
  </button>

  <input id="gcSlideInterval" class="gc-number" type="number" min="1" step="1" value="3" inputmode="numeric">
  <span class="gc-unit">秒</span>

  <button id="gcSlidePrev" class="gc-btn" type="button" aria-label="前へ">◀</button>
  <button id="gcSlideNext" class="gc-btn" type="button" aria-label="次へ">▶</button>
</div>

<div class="gallery-controls" role="group" aria-label="ギャラリーレイアウト設定">
  <div class="gc-row">
    <label class="gc-label" for="gcLayout">表示</label>
    <select id="gcLayout" class="gc-select">
      <option value="grid">グリッド</option>
      <option value="list">リスト</option>
    </select>
  </div>

  <div class="gc-row" id="gcColsRow">
    <label class="gc-label" for="gcCols">列数</label>
    <input id="gcCols" class="gc-range" type="range" min="1" max="6" step="1" value="3">
    <span id="gcColsValue" class="gc-badge">3</span>
  </div>
</div>

<ul class="gallery-grid" id="galleryGrid">
{% for p in site.data.gallery %}
  <li class="gallery-card">
    <a class="gallery-link" href="{{ p.src }}"
       data-full="{{ p.src }}"
       data-alt="{{ p.alt | escape }}"
       data-desc="{{ p.desc | default: '' | strip_newlines | escape }}">
      <img class="gallery-img" src="{{ p.src }}" alt="{{ p.alt }}" loading="lazy" decoding="async">
    </a>
    {% if p.desc %}
      <p class="gallery-desc">{{ p.desc }}</p>
    {% endif %}
  </li>
{% endfor %}
</ul>

<dialog class="img-modal" id="imgModal" aria-label="画像プレビュー">
  <div class="img-modal__inner">
    <button class="img-modal__close" id="imgModalClose" aria-label="閉じる">×</button>

    <figure class="img-modal__figure">
      <img id="imgModalImg" class="img-modal__img" alt="">
      <figcaption class="img-modal__cap">
        <div id="imgModalAlt" class="img-modal__title"></div>
        <div id="imgModalDesc" class="img-modal__desc"></div>
      </figcaption>
    </figure>

    <div class="img-modal__actions">
      <a id="imgModalOpen" class="img-modal__link" href="#" target="_blank" rel="noopener noreferrer">元画像を開く</a>
    </div>
  </div>
</dialog>

<a href="#top" class="pagetop">
  <img src="/top2.png" alt="Page Top">
</a>

<script>
(function () {
  var grid = document.getElementById('galleryGrid');
  var layoutSel = document.getElementById('gcLayout');
  var colsRange = document.getElementById('gcCols');
  var colsValue = document.getElementById('gcColsValue');
  var colsRow = document.getElementById('gcColsRow');

  // ===== Image Modal Preview =====
  var modal = document.getElementById('imgModal');
  var modalImg = document.getElementById('imgModalImg');
  var modalAlt = document.getElementById('imgModalAlt');
  var modalDesc = document.getElementById('imgModalDesc');
  var modalOpen = document.getElementById('imgModalOpen');
  var modalClose = document.getElementById('imgModalClose');


  var slideToggle = document.getElementById('gcSlideToggle');
  var slideInterval = document.getElementById('gcSlideInterval');
  var slidePrev = document.getElementById('gcSlidePrev');
  var slideNext = document.getElementById('gcSlideNext');

  // 万一要素が取れない場合の安全策（テンプレ編集途中でも落ちない）
  if (!grid || !layoutSel || !colsRange || !colsValue || !colsRow
      || !modal || !modalImg || !modalAlt || !modalDesc || !modalOpen || !modalClose
      || !slideToggle || !slideInterval || !slidePrev || !slideNext) {
    return;
  }

  function apply(layout, cols) {
    grid.classList.toggle('is-list', layout === 'list');
    grid.classList.toggle('is-grid', layout !== 'list');

    var isList = layout === 'list';
    colsRow.style.opacity = isList ? '0.5' : '1';
    colsRange.disabled = isList;

    var n = parseInt(cols, 10);
    if (!Number.isFinite(n)) n = 3;
    n = Math.max(1, Math.min(6, n));

    grid.style.setProperty('--gallery-cols', String(n));
    colsRange.value = String(n);
    colsValue.textContent = String(n);

    try {
      localStorage.setItem('gallery.layout', layout);
      localStorage.setItem('gallery.cols', String(n));
    } catch (e) {}
  }

  // 初期値
  var savedLayout = 'grid';
  var savedCols = '3';
  try {
    savedLayout = localStorage.getItem('gallery.layout') || 'grid';
    savedCols = localStorage.getItem('gallery.cols') || '3';
  } catch (e) {}

  layoutSel.value = (savedLayout === 'list') ? 'list' : 'grid';
  colsRange.value = savedCols;
  apply(layoutSel.value, colsRange.value);

  layoutSel.addEventListener('change', function () {
    apply(layoutSel.value, colsRange.value);
  });

  colsRange.addEventListener('input', function () {
    apply(layoutSel.value, colsRange.value);
  });

  function openModal(src, alt, desc) {
    modalImg.src = src;
    modalImg.alt = alt || '';
    modalAlt.textContent = alt || '';
    modalDesc.textContent = desc || '';
    modalOpen.href = src;

    if (typeof modal.showModal === 'function') {
      modal.showModal();
    } else {
      modal.setAttribute('open', '');
    }
    document.documentElement.classList.add('modal-open');
  }

  function closeModal() {
    if (typeof modal.close === 'function') {
      modal.close();
    } else {
      modal.removeAttribute('open');
    }
    document.documentElement.classList.remove('modal-open');
    modalImg.src = '';
    if (slideshow && slideshow.stop) slideshow.stop();
  }

  // 画像クリックでモーダル表示
  grid.addEventListener('click', function (e) {
    var a = e.target && e.target.closest ? e.target.closest('a.gallery-link') : null;
    if (!a) return;

    e.preventDefault();

    var src = a.getAttribute('data-full') || a.getAttribute('href');
    var alt = a.getAttribute('data-alt') || '';
    var desc = a.getAttribute('data-desc') || '';

    openModal(src, alt, desc);
  });

  modalClose.addEventListener('click', function () {
    closeModal();
  });

  modal.addEventListener('click', function (e) {
    if (e.target === modal) closeModal();
  });

  modal.addEventListener('cancel', function (e) {
    e.preventDefault();
    closeModal();
  });
  
  
  // ===== Thumb paint stabilizer（グリッドで空白になりがちな画像の描画を安定化）=====
  function markLoaded(img) {
    img.classList.add('is-loaded');
  }
  function markError(img) {
    img.classList.add('is-error');
  }
  function ensurePaint(img) {
    if (!img) return;

    // すでに読み込み済みなら decode を促す（描画が遅れて空白になる対策）
    if (img.complete && img.naturalWidth > 0) {
      if (img.decode) img.decode().catch(function(){});
      markLoaded(img);
      return;
    }

    img.addEventListener('load', function () {
      if (img.decode) img.decode().catch(function(){});
      markLoaded(img);
    }, { once: true });

    img.addEventListener('error', function () {
      markError(img);
    }, { once: true });
  }

  var imgs = Array.prototype.slice.call(grid.querySelectorAll('img.gallery-img'));
  imgs.forEach(ensurePaint);

  // 画面に近づいたら eager + decode（lazyの誤判定/後回しを抑制）
  if ('IntersectionObserver' in window) {
    var io = new IntersectionObserver(function (entries) {
      entries.forEach(function (ent) {
        if (!ent.isIntersecting) return;
        var img = ent.target;
        img.loading = 'eager';     // ここで “表示するなら今” を明示
        ensurePaint(img);
        io.unobserve(img);
      });
    }, { root: null, rootMargin: '300px 0px', threshold: 0.01 });

    imgs.forEach(function (img) { io.observe(img); });
  } else {
    // フォールバック：全部 eager
    imgs.forEach(function (img) { img.loading = 'eager'; ensurePaint(img); });
  }

    // ===== Slideshow Module（n秒周期で全写真を順に表示）=====
  var links = Array.prototype.slice.call(grid.querySelectorAll('a.gallery-link'));
  var currentIndex = -1;

  function clampInt(n, min, max, fallback) {
    var v = parseInt(String(n), 10);
    if (!Number.isFinite(v)) v = fallback;
    v = Math.max(min, Math.min(max, v));
    return v;
  }

  function getSlide(i) {
    var a = links[i];
    if (!a) return null;
    var src = a.getAttribute('data-full') || a.getAttribute('href');
    var alt = a.getAttribute('data-alt') || '';
    var desc = a.getAttribute('data-desc') || '';
    return { src: src, alt: alt, desc: desc };
  }

function openByIndex(i) {
  if (!links.length) return;

  var idx = ((i % links.length) + links.length) % links.length;
  var s = getSlide(idx);
  if (!s) return;

  // ★ここが正しい
  currentIndex = idx;
  openModal(s.src, s.alt, s.desc);

  // 次を軽く先読み（空白ちらつき軽減）
  var next = getSlide((idx + 1) % links.length);
  if (next && next.src) {
    var pre = new Image();
    pre.decoding = 'async';
    pre.loading = 'eager';
    pre.src = next.src;
    if (pre.decode) pre.decode().catch(function(){});
  }
}

  // openModal を呼ぶ箇所（既存のクリック）で currentIndex も更新する
  // ※あなたの「grid.addEventListener('click', ...)」内の openModal(...) 呼び出しを以下に置き換え
  // openModal(src, alt, desc);
  // ↓
  // currentIndex = Math.max(0, links.indexOf(a));
  // openModal(src, alt, desc);

  var slideshow = (function () {
    var running = false;
    var timer = 0;
    var intervalMs = 3000;
    var lastTickAt = 0;

    function loadSettings() {
      try {
        var saved = localStorage.getItem('gallery.slideshow.intervalSec');
        if (saved) intervalMs = clampInt(saved, 1, 3600, 3) * 1000;
      } catch (e) {}
      slideInterval.value = String(Math.round(intervalMs / 1000));
    }

    function saveSettings() {
      try {
        localStorage.setItem('gallery.slideshow.intervalSec', String(Math.round(intervalMs / 1000)));
      } catch (e) {}
    }

    function render() {
      slideToggle.textContent = running ? '停止' : '開始';
      slideToggle.setAttribute('aria-pressed', running ? 'true' : 'false');
    }

    function stop() {
      running = false;
      if (timer) clearTimeout(timer);
      timer = 0;
      render();
    }

    function stepNext() {
      if (!links.length) return;
      if (currentIndex < 0) currentIndex = 0;
      openByIndex((currentIndex + 1) % links.length);
    }

    function stepPrev() {
      if (!links.length) return;
      if (currentIndex < 0) currentIndex = 0;
      openByIndex((currentIndex - 1 + links.length) % links.length);
    }

    function tick() {
      if (!running) return;

      // タブ非表示中は進めない（復帰時に暴発しにくくする）
      if (document.hidden) {
        timer = setTimeout(tick, 500);
        return;
      }

      var now = Date.now();
      // 初回・または間隔変更時に極端に早く進まないようガード
      if (lastTickAt && now - lastTickAt < Math.max(250, intervalMs * 0.6)) {
        timer = setTimeout(tick, 250);
        return;
      }
      lastTickAt = now;

      stepNext();
      timer = setTimeout(tick, intervalMs);
    }

    function start() {
      if (running) return;
      if (!links.length) return;

      running = true;
      render();

      // モーダルが閉じている状態なら、現在 or 先頭から開いて開始
      if (!modal.hasAttribute('open')) {
        openByIndex(currentIndex >= 0 ? currentIndex : 0);
      }

      lastTickAt = 0;
      if (timer) clearTimeout(timer);
      timer = setTimeout(tick, intervalMs);
    }

    function setIntervalSec(sec) {
      intervalMs = clampInt(sec, 1, 3600, 3) * 1000;
      slideInterval.value = String(Math.round(intervalMs / 1000));
      saveSettings();

      if (running) {
        // 即反映（タイマー再起動）
        if (timer) clearTimeout(timer);
        lastTickAt = 0;
        timer = setTimeout(tick, intervalMs);
      }
    }

    // public
    loadSettings();
    render();

    return {
      start: start,
      stop: stop,
      toggle: function () { running ? stop() : start(); },
      next: stepNext,
      prev: stepPrev,
      setIntervalSec: setIntervalSec,
      isRunning: function () { return running; },
    };
  })();

  // controls wiring
  slideToggle.addEventListener('click', function () {
    slideshow.toggle();
  });

  slideInterval.addEventListener('change', function () {
    slideshow.setIntervalSec(slideInterval.value);
  });

  slidePrev.addEventListener('click', function () {
    slideshow.prev();
  });

  slideNext.addEventListener('click', function () {
    slideshow.next();
  });

  // キーボード操作（任意）：←/→で前後、Spaceで開始/停止
  document.addEventListener('keydown', function (e) {
    // 入力中は邪魔しない
    var t = e.target;
    var isTyping = t && (t.tagName === 'INPUT' || t.tagName === 'TEXTAREA' || t.isContentEditable);
    if (isTyping) return;

    if (!modal.hasAttribute('open')) return;

    if (e.key === 'ArrowRight') { e.preventDefault(); slideshow.next(); }
    if (e.key === 'ArrowLeft')  { e.preventDefault(); slideshow.prev(); }
    if (e.key === ' ')          { e.preventDefault(); slideshow.toggle(); }
  });

  // モーダルを閉じたら自動再生も止める（暴走防止）
  // ※あなたの closeModal() の最後にこれを1行追加してもOK：
  // slideshow.stop();

})();
</script>

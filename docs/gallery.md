---
layout: default
title: Gallery
permalink: /gallery.html
---

# Gallery

ここは、とまが撮影した、お気に入り写真を載せています。

<hr class="hr3">
<hr class="hr2">

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

  function apply(layout, cols) {
    // layout
    grid.classList.toggle('is-list', layout === 'list');
    grid.classList.toggle('is-grid', layout !== 'list');

    // cols（listの時は操作不可にする）
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

  // 初期値（保存があれば優先）
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
})();

  // ===== Image Modal Preview =====
  var modal = document.getElementById('imgModal');
  var modalImg = document.getElementById('imgModalImg');
  var modalAlt = document.getElementById('imgModalAlt');
  var modalDesc = document.getElementById('imgModalDesc');
  var modalOpen = document.getElementById('imgModalOpen');
  var modalClose = document.getElementById('imgModalClose');

  function openModal(src, alt, desc) {
    modalImg.src = src;
    modalImg.alt = alt || '';
    modalAlt.textContent = alt || '';
    modalDesc.textContent = desc || '';
    modalOpen.href = src;

    // <dialog> が使える環境
    if (typeof modal.showModal === 'function') {
      modal.showModal();
    } else {
      // フォールバック
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

    // 画像を解放（メモリ節約）
    modalImg.src = '';
  }

  // 画像クリックをモーダルに差し替え
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

  // 背景クリックで閉じる（dialogの外側をクリック）
  modal.addEventListener('click', function (e) {
    // dialog自体（背景）をクリックした場合のみ閉じる
    if (e.target === modal) closeModal();
  });

  // Escで閉じる（dialogのcancelイベント）
  modal.addEventListener('cancel', function (e) {
    e.preventDefault();
    closeModal();
  });

</script>

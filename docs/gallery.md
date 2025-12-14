---
layout: default
title: Gallery
permalink: /gallery.html
---

# Gallery

ここは、とまが撮影した、お気に入り写真を載せています。

<hr class="hr3">
<hr class="hr2">

<ul class="gallery-grid">
{% for p in site.data.gallery %}
  <li class="gallery-card">
    <a class="gallery-link" href="{{ p.src }}" target="_blank" rel="noopener noreferrer">
      <img class="gallery-img" src="{{ p.src }}" alt="{{ p.alt }}" loading="lazy" decoding="async">
    </a>
    {% if p.desc %}
      <p class="gallery-desc">{{ p.desc }}</p>
    {% endif %}
  </li>
{% endfor %}
</ul>

<a href="#top" class="pagetop">
  <img src="top2.png" alt="Page Top">
</a>

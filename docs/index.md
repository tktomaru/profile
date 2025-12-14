---
layout: default
title: Webサイト紹介
permalink: /index.html
---

# Webサイト紹介

作成したWebサイトを紹介します。

<hr class="hr3">
<hr class="hr2">

## WebPage（作成サイト）

<ul class="card-grid">
{% for s in site.data.sites %}
  <li class="card">
    <div class="card-head">
      <h3 class="card-title">{{ s.name }}</h3>
      <a class="card-url" href="{{ s.url }}" target="_blank" rel="noopener noreferrer">
        {{ s.url }}
      </a>
    </div>

    {% if s.desc %}
      <p class="card-desc">{{ s.desc }}</p>
    {% endif %}

    {% if s.tags and s.tags.size > 0 %}
      <div class="tags">
        {% for t in s.tags %}
          <span class="tag">{{ t }}</span>
        {% endfor %}
      </div>
    {% endif %}
  </li>
{% endfor %}
</ul>

<hr class="hr3">
<hr class="hr2">

## Links（リンク集）
<!-- 既存のリンク集があるなら、ここはそのままHTMLで貼ってもOK -->
<div class="link-list">
  <a class="link-item" href="https://github.com/tktomaru" target="_blank" rel="noopener noreferrer">
    <img src="./images/banner_github.png" alt="リンクバナー" width="88" height="31"> github
  </a>
  <a class="link-item" href="https://x.com/tk_tomaru" target="_blank" rel="noopener noreferrer">
    <img src="./images/banner_twitter.png" alt="リンクバナー" width="88" height="31"> Twitterサイト
  </a>
  <a class="link-item" href="https://tukutano.jp/" target="_blank" rel="noopener noreferrer">
    <img src="./images/banner.png" alt="リンクバナー" width="88" height="31"> つくたのブログ
  </a>
  <a class="link-item" href="https://tktomaru.hatenablog.jp/" target="_blank" rel="noopener noreferrer">
    <img src="./images/banner_hatena.png" alt="リンクバナー" width="88" height="31"> はてなブログ
  </a>
</div>

---
layout: default
title: About
permalink: /about.html
---

# このサイトは？

とまによる個人サイトです。  
作成したサイトなどを紹介します。

<hr class="hr3">

## About Link

### 当サイトへのリンクについて
{{ site.data.about.link_note }}

<div class="about-card">
  <div class="about-card-head">About Link</div>

  <div class="about-card-body">
    <div class="about-banner">
      <img
        src="{{ site.data.about.banner_img }}"
        alt="{{ site.data.about.banner_alt }}"
        width="{{ site.data.about.banner_width }}"
        height="{{ site.data.about.banner_height }}"
      >
    </div>

    <ul class="about-list">
      <li>・{{ site.data.about.site_title }}</li>
      <li>・管理人名 {{ site.data.about.owner_name }}</li>
      <li>・URL <a href="{{ site.data.about.site_url }}" target="_blank" rel="noopener noreferrer">{{ site.data.about.site_url }}</a></li>
    </ul>
  </div>
</div>

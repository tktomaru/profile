---
layout: default
title: Info
permalink: /info.html
---

# Information

お知らせ・更新履歴など

<hr class="hr3">
<hr class="hr2">

<ul class="timeline">
{% for u in site.data.updates %}
  <li class="timeline-item">
    <div class="timeline-date">{{ u.date }}</div>

    <div class="timeline-body">
      <div class="timeline-title">{{ u.title }}</div>

      {% if u.items and u.items.size > 0 %}
      <ul class="timeline-sub">
        {% for it in u.items %}
          <li>{{ it }}</li>
        {% endfor %}
      </ul>
      {% endif %}
    </div>
  </li>
{% endfor %}
</ul>

<a href="#top" class="pagetop">
  <img src="/top2.png" alt="Page Top">
</a>

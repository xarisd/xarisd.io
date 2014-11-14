---
layout: page
title: Presentations
permalink: /presentations/
---

Here you can find my presentations

<ul>
{% for presentation in site.data.presentations %}
  <li>
    <a href="https://github.com/{{ member.thessrb }}">
      {{ presentation.name }}
    </a>
  </li>
{% endfor %}
</ul>

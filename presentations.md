---
layout: page
title: Presentations
permalink: /presentations/
---

<p class="intro">
  Here you will find my presentations. They are mostly about things I like to talk about during local meetup sessions and events.
</p>


<ul class="showcase-list">
{% assign prs = site.data.presentations | sort: 'event.date' | reverse %}
{% for presentation in prs %}
  <li class="showcase-item">
    <h3 class="title">{{ presentation.name }}</h3>
    <p class="event-info">
      Last presented at <a href="{{ presentation.event.url }}" target="{{ presentation.code }}_event">{{presentation.event.name}} on {{presentation.event.date}}</a></p>
    <p class="description">
      {{ presentation.description }}
    </p>
    <p class="links">
      <a class="preview" href="{{ presentation.url }}" target="{{ presentation.code }}">View it online</a> or
      <a class="source" href="{{ presentation.source }}" target="{{ presentation.code }}_github">Explore the source code</a>
    </p>
  </li>
{% endfor %}
</ul>

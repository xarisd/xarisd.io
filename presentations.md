---
layout: page
title: Presentations
permalink: /presentations/
---

<p class="intro">
  My presentations are mostly about stuff I am passionate about and have been presented in local meetup sessions and events.
</p>


<ul class="showcase-list favorites">
{% assign prs = site.data.presentations | where:"favorite",true | sort: 'favorite_order' | reverse %}
{% for presentation in prs %}
  <li class="showcase-item favorite">
    <h3 class="title">{{ presentation.name }}</h3>
    <p class="event-info">
      Last presented at <a href="{{ presentation.event.url }}" rel="nofollow">{{presentation.event.name}} <!--on {{presentation.event.date}} --></a></p>
    <p class="description">
      {{ presentation.description }}
    </p>
    <p class="links">
      <!--<a class="preview" href="{{ presentation.url }}">View it online</a> or-->
      <a class="source" href="{{ presentation.source }}" rel="nofollow">Explore the source code</a>
    </p>
  </li>
{% endfor %}
</ul>

<ul class="showcase-list">
{% assign prs = site.data.presentations | where:"favorite",false | sort: 'event.date' | reverse %}
{% for presentation in prs %}
  <li class="showcase-item">
    <h3 class="title">{{ presentation.name }}</h3>
    <p class="event-info">
      Last presented at <a href="{{ presentation.event.url }}" rel="nofollow">{{presentation.event.name}} <!--on {{presentation.event.date}} --> </a></p>
    <p class="description">
      {{ presentation.description }}
    </p>
    <p class="links">
      <!-- <a class="preview" href="{{ presentation.url }}">View it online</a> or -->
      <a class="source" href="{{ presentation.source }}" rel="nofollow">Explore the source code</a>
    </p>
  </li>
{% endfor %}
</ul>

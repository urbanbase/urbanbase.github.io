---
bg: "skin_4.png"
layout: page
permalink: /dev/
title: "Developer"
crawlertitle: "Developer"
summary: ""
active: dev
---

{% for tag in site.tags %}
  {% assign t = tag | first %}
  {% assign posts = tag | last %}

  {% for post in posts  limit: 1 %}
    {% if post.tags contains t %}
      {% if post.categories contains "dev" %}
        <h2 class="category-key" id="{{ t | downcase }}">{{ t | capitalize }}</h2>
      {% endif %}
    {% endif %}
  {% endfor %}

  <ul class="year">
    {% for post in posts %}
      {% if post.tags contains t %}
        {% if post.categories contains "dev" %}
          <li>
            {% if post.lastmod %}
              <a href="{{ post.url | relative_url}}">{{ post.title }}</a>
              <span class="date">{{ post.lastmod | date: "%Y-%m-%d"  }}</span>
            {% else %}
              <a href="{{ post.url | relative_url}}">{{ post.title }}</a>
              <span class="date">{{ post.date | date: "%Y-%m-%d"  }}</span>
            {% endif %}
          </li>
        {% endif %}
      {% endif %}
    {% endfor %}
  </ul>

{% endfor %}

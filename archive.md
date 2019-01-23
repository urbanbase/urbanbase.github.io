---
bg: "default_background.jpeg"
layout: page
permalink: /tech/
title: "Technology"
crawlertitle: "Technology"
summary: ""
active: tech
---

{% for tag in site.tags %}
  {% assign t = tag | first %}
  {% assign posts = tag | last %}

  <h2 class="category-key" id="{{ t | downcase }}">{{ t | capitalize }}</h2>

  <ul class="year">
    {% for post in posts %}
      {% if post.tags contains t %}
        {% if post.categories contains "Technology" %}
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

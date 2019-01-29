---
bg: "skin_3.png"
layout: page
permalink: /tech/
title: "Tech"
crawlertitle: "Tech"
summary: ""
active: tech
---


<div id="search-container">
<center>
<input type="text" id="search-input" placeholder="Search..." style="width:500px;">
</center>
<ul id="results-container"></ul>
</div>

<!-- Script pointing to jekyll-search.js -->
<script src="{{site.baseurl}}/dest/jekyll-search.js" type="text/javascript"></script>

<script type="text/javascript">
      SimpleJekyllSearch({
        searchInput: document.getElementById('search-input'),
        resultsContainer: document.getElementById('results-container'),
        json: '{{ site.baseurl }}/search2.json',
        searchResultTemplate: '<li><a href="{url}" title="{desc}">{title}</a></li>',
        noResultsText: '검색결과가 없습니다.',
        limit: 10,
        fuzzy: false,
        exclude: ['Welcome']
      })
</script>

<br>
<br>

{% for tag in site.tags %}
  {% assign t = tag | first %}
  {% assign posts = tag | last %}

  {% for post in posts  limit: 1 %}
    {% if post.tags contains t %}
      {% if post.categories contains "tech" %}
  <h2 class="category-key" id="{{ t | downcase }}">{{ t | capitalize }}</h2>
      {% endif %}
    {% endif %}
  {% endfor %}

  <ul class="year">
    {% for post in posts %}
      {% if post.tags contains t %}
        {% if post.categories contains "tech" %}
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

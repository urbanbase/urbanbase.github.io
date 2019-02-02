---
layout: main
permalink: /tags/
---

<div class="wrapper">
    <center>
    <h3>Tags List</h3>
    {% unless page.cover %} {% include page_divider.html %} {% endunless %}
    /
    {% for tag in site.tags %}
        {% assign t = tag | first %}
        <a class="tag_list" href="/tags/{{ t }}">{{ t }}</a> /
    {% endfor %}
    </center>
</div>
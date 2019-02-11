---
layout: main
permalink: /tags/
---

<div class="wrapper">
    <center>
    <h2>Tags List</h2>
    {% unless page.cover %} {% include page_divider.html %} {% endunless %}
    <br>
    /
    {% for tag in site.tags %}
        {% assign t = tag | first %}
        <a class="tag_list" href="/tags/{{ t }}">{{ t }}</a> /
    {% endfor %}
    </center>
</div>
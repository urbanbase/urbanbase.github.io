---
layout: main
permalink: /tags/
---

<div class="wrapper">
    <center>
    <h3>Tags List</h3>
    </center>
    {% unless page.cover %} {% include page_divider.html %} {% endunless %}
    <br>
    {% for tag in site.tags %}
        {% assign t = tag | first %}
        <a class="post" href="/tags/{{ t }}">{{ t }}</a>
        <br>
    {% endfor %}

</div>
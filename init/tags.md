---
layout: main
permalink: /tags/
---

<div class="wrapper">

    <h3>Tags</h3>
    <br>
    <br>
    {% for tag in site.tags %}
        {% assign t = tag | first %}
        <a class="post" href="/tags/{{ t }}">{{ t }}</a>
        <br>
    {% endfor %}

</div>
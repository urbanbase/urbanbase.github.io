---
layout: main
permalink: /tags/
---

<div class="wrapper">

    <strong>Tags</strong>
    <br>
    <br>
    {% for tag in site.tags %}
        {% assign t = tag | first %}
        <a class="post" href="https://urbanbase.github.io/tags/{{ t }}">{{ t }}</a>
        <br>
    {% endfor %}

</div>
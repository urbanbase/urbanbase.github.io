---
layout: main
permalink: /tags/
---

<div class="wrapper">

    <strong>Tags</strong>
    <br>
    {% for tag in site.tags %}
        {% assign t = tag | first %}
        {{ t }}
        <br>
    {% endfor %}

</div>
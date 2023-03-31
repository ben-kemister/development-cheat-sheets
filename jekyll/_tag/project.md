---
title: "Tag: Project"
tag: project
layout: default
---
<div class="post">
    <h1>Tag: {{ page.tag }}</h1>
    <ul>
        {% for p in site.documents %}
        {% if p.tags contains page.tag %}
        <li><a href="{{ p.url | relative_url}}">{{ p.title }}</a>
            {% if p.status != null %}
            - {{ p.status }}
            {% endif %}
        </li>
        {% endif %}
        {% endfor %}
    </ul>
</div>
<hr>

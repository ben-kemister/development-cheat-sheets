---
layout: default
---

{% include navigation.html %}

{% capture foo %}

{% if page.showTOC %}
  <details>
    <summary>Table of Contents</summary>
    
    {% include toc.html html=content %}
  </details>
{% endif %}

{{ content }}
{% endcapture %}

{{ foo | markdownify }}


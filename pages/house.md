---
layout: house
permalink: /house/
---

# 부동산

<div class="section-index">
    <hr class="panel-line">
    {% for house in site.house  %}        
    <div class="entry">
    <h5><a href="{{ house.url | prepend: site.baseurl }}">{{ house.title }}</a></h5>
    <p>{{ house.description }}</p>
    </div>{% endfor %}
</div>

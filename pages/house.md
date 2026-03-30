---
layout: house
permalink: /house/
---

# 부동산

<div class="section-index">
    <hr class="panel-line">
    {% for post in site.categories['부동산']  %}        
    <div class="entry">
    <h5><a href="{{ post.url | prepend: site.baseurl }}">{{ post.title }}</a></h5>
    <p>{{ post.description }}</p>
    </div>{% endfor %}
</div>

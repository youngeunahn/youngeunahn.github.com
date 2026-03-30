---
layout: legacy
permalink: /legacy/
---

# 레거시 프로젝트

<div class="section-index">
    <hr class="panel-line">
    {% for post in site.categories['스프링부트_레거시프로젝트']  %}        
    <div class="entry">
    <h5><a href="{{ post.url | prepend: site.baseurl }}">{{ post.title }}</a></h5>
    <p>{{ post.description }}</p>
    </div>{% endfor %}
</div>

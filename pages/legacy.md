---
layout: legacy
permalink: /legacy/
---

# 레거시 프로젝트

<div class="section-index">
    <hr class="panel-line">
    {% for legacy in site.legacy  %}        
    <div class="entry">
    <h5><a href="{{ legacy.url | prepend: site.baseurl }}">{{ legacy.title }}</a></h5>
    <p>{{ legacy.description }}</p>
    </div>{% endfor %}
</div>
